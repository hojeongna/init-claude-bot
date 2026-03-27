# 스케줄러 데몬 관리

_Python 스케줄러 데몬으로 크론 잡을 추가/수정/삭제/조회한다. tmux 기반으로 메인 Claude 세션에 결과를 주입하는 구조._

---

## 0. 환경 체크 (처음 쓰는 유저)

스케줄러를 쓰려면 **tmux + LaunchAgent**가 필요하다. 먼저 확인:

### tmux 세션 확인

```bash
tmux ls
```

메인 Claude 세션이 tmux 안에서 돌고 있어야 한다. 없으면 세팅부터:

```bash
# 1. tmux 설치 (없으면)
brew install tmux

# 2. tmux 세션 생성 + Claude 실행
tmux new-session -d -s my-bot-main
tmux send-keys -t my-bot-main "cd /path/to/my-bot && claude --channels plugin:discord@claude-plugins-official --dangerously-skip-permissions" Enter
```

> **왜 tmux인가?** 스케줄러가 크론 결과를 Claude 세션에 전달하려면 `tmux send-keys`로 텍스트를 주입해야 한다. tmux 없이 돌리면 크론 결과를 받을 수 없다.

### LaunchAgent 확인

스케줄러 데몬이 LaunchAgent로 자동시작/자동복구되는지 확인:

```bash
launchctl list | grep scheduler
```

없으면 plist 생성 필요 — 아래 "LaunchAgent 설정" 섹션 참고.

---

## 1. 아키텍처

```
[Python 스케줄러 데몬] ← LaunchAgent로 항상 살아있음
  ├─ 30초마다 tick() → 시간 조건 체크
  ├─ 실행 방식:
  │   ├─ PY: Python 스크립트 (subprocess) — 빠름, 토큰 절약
  │   └─ AI: claude -p (새 세션) — 판단/분석 필요할 때
  ├─ 응답 모드:
  │   ├─ silent: 조용히 처리, 로그만
  │   ├─ conditional: 새 데이터 있을 때만 tmux 주입
  │   └─ notify: 항상 tmux 주입
  ├─ tmux 주입: "Cron : {메시지}" 형태로 메인 세션에 전달
  └─ 3중 로깅: cron-status.json + cron-log.json + cron.log
```

### 핵심 파일

- `scripts/scheduler_daemon.py` — 스케줄러 본체 (크론 메타, 함수, tick 로직)
- `dashboard/cron-status.json` — 크론별 최신 상태 (대시보드 CRON 탭)
- `dashboard/cron-schedule.json` — 크론 목록 메타데이터 (데몬 시작 시 생성)
- `dashboard/cron-log.json` — 최근 200개 실행 이력
- `memory/cron.log` — 텍스트 append 로그

### LaunchAgent 설정

plist 위치: `~/Library/LaunchAgents/com.ttongtting.scheduler.plist`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>com.ttongtting.scheduler</string>
    <key>ProgramArguments</key>
    <array>
        <string>/opt/homebrew/bin/python3</string>
        <string>/path/to/my-bot/scripts/scheduler_daemon.py</string>
    </array>
    <key>WorkingDirectory</key>
    <string>/path/to/my-bot</string>
    <key>KeepAlive</key>
    <true/>
    <key>StandardOutPath</key>
    <string>/path/to/my-bot/memory/scheduler.log</string>
    <key>StandardErrorPath</key>
    <string>/path/to/my-bot/memory/scheduler.log</string>
</dict>
</plist>
```

등록: `launchctl load ~/Library/LaunchAgents/com.ttongtting.scheduler.plist`

---

## 2. 추천 크론 (분류별)

유저 상황에 맞게 골라서 설정한다. 전부 넣을 필요 없음.

### 웹 모니터링

특정 웹사이트/피드의 변경을 감지해서 알림.

- **RSS/피드 체크** — 블로그, 뉴스, 릴리스 노트 등 RSS 피드 새 글 감지 (PY, conditional)
- **웹페이지 변경 감지** — 특정 URL의 변경 여부 체크 (PY, conditional)
- **API 상태 모니터링** — 외부 서비스 health check, 다운타임 감지 (PY, conditional)

### 알림/리마인더

시간 기반 알림. 조건 체크 후 메인 세션에 주입.

- **모닝 브리핑** — 캘린더/날씨/뉴스 등 아침 요약 (PY, notify, 평일만)
- **리마인더** — 정해진 시간에 메시지 전달 (PY, notify)
- **날짜 변경 알림** — 자정에 날짜 전달, 일일 리셋 트리거 (PY, silent)

### 시스템 유지보수

봇 자체를 건강하게 유지.

- **하트비트** — 주기적 상태 체크 + 설정 파일 업데이트 (AI, silent)
- **설정 최신화** — 설정 파일 리뷰 및 outdated 정리 (AI, silent)
- **로그 정리** — 오래된 로그 파일 정리, 디스크 관리 (PY, silent)

### 데이터 수집/가공

주기적으로 데이터를 수집하고 가공.

- **데일리 다이제스트** — 하루치 데이터 모아서 요약 전송 (PY+AI, notify)
- **백업** — 중요 파일/데이터 주기적 백업 (PY, silent)
- **리포트 생성** — 주간/월간 통계 리포트 자동 생성 (AI, notify)

---

## 3. 리마인더 (1회성 / 반복)

스케줄러 데몬에 등록할 필요 없는 가벼운 리마인더는 Claude Code 내장 크론을 쓴다.

### 1회성 리마인더

"내일 1시에 XX 알려줘", "3시에 회의 리마인드" 같은 요청:

```
CronCreate(cron="3 13 28 3 *", prompt="리마인더 내용", recurring=false)
```

- `recurring: false`로 설정하면 1번 실행 후 자동 삭제
- 분/시/일/월을 정확히 지정

### 반복 리마인더

"매일 9시에 알려줘", "평일 7시에 리마인드" 같은 요청:

```
CronCreate(cron="3 9 * * 1-5", prompt="리마인더 내용", recurring=true)
```

- 7일 후 자동 만료 (세션 수명 제한)
- 분은 0, 30 피해서 설정 (서버 부하 분산)

### 주의사항

- **세션 전용**: Claude 세션이 종료되면 사라짐
- **영구 리마인더**: 세션 독립적이어야 하면 스케줄러 데몬에 크론으로 등록할 것
- **tmux 필수**: 세션을 유지하려면 tmux 안에서 Claude를 돌려야 함

---

## 4. 크론 추가

### Step 1 — 실행 방식 결정

| 케이스 | 방식 | 이유 |
|--------|------|------|
| 외부 API, 데이터 수집, 파일 처리 | PY | 빠르고 토큰 절약 |
| 파일 분석, 요약, 판단, 텍스트 생성 | AI | Claude 판단력 필요 |
| 수집 후 코멘트 붙여서 전송 | PY + tmux 주입 | 수집은 PY, 코멘트는 메인 세션이 |

### Step 2 — 응답 모드 결정

| 케이스 | 모드 | 이유 |
|--------|------|------|
| 파일 업데이트만, 유저 알림 불필요 | silent | 노이즈 제거 |
| 새 데이터 있을 때만 알림 | conditional | 대부분의 모니터링에 적합 |
| 매번 결과를 전달해야 함 | notify | 정해진 시간 알림에 적합 |

### Step 3 — scheduler_daemon.py 수정 (3곳)

**(a) `CRON_SCHEDULE` 배열에 메타데이터 추가:**

```python
{"key": "new_cron", "name": "한글 이름", "schedule": "분 시 일 월 요일", "method": "python|claude -p", "response": "silent|conditional|notify", "desc": "설명"},
```

**(b) 크론 함수 작성:**

```python
def new_cron():
    """설명"""
    try:
        # PY 방식:
        result = run_script(str(BOT_DIR / "scripts/new_cron_script.py"), timeout=30)
        if "조건":
            tmux_inject("NEW_CRON: 내용")
            log_status("new_cron", "sent")
        else:
            log_status("new_cron", "no_new")

        # 또는 AI 방식:
        claude_run("프롬프트", timeout=60)
        log_status("new_cron", "ok")
    except Exception as e:
        log_status("new_cron", "error", str(e))
```

**(c) `Scheduler.tick()` 메서드에 시간 조건 추가:**

```python
# ── 새 크론: 설명 ──
if 시간_조건:
    self.run_once_per_minute("new_cron", new_cron)
```

### Step 4 — 테스트 (필수)

크론 추가 후 **반드시 수동 테스트**를 실행한다. 배포 전 검증 없이 재시작 금지.

```bash
# 크론 함수만 단독 실행
python3 -c "import sys; sys.path.insert(0, 'scripts'); from scheduler_daemon import new_cron; new_cron()"
```

테스트 체크리스트:
- [ ] 에러 없이 실행되는가
- [ ] 로그에 상태가 기록되는가 (`dashboard/cron-status.json` 확인)
- [ ] tmux 주입이 필요한 크론이면, 주입 메시지가 정상인가
- [ ] 유저에게 테스트 결과를 보여주고 OK 받았는가

### Step 5 — TRIGGERS.md 업데이트

tmux 주입이 있는 크론이면 메인 세션이 받아서 처리할 트리거를 등록:

```markdown
### NEW_CRON (크론 주입)

- **트리거:** `Cron : NEW_CRON:` 수신 (스케줄러 tmux 주입)
- **실행:** 받은 내용 처리 → 디스코드 전달
```

### Step 6 — 스케줄러 재시작

```bash
launchctl unload ~/Library/LaunchAgents/com.ttongtting.scheduler.plist
launchctl load ~/Library/LaunchAgents/com.ttongtting.scheduler.plist
```

재시작 후 `dashboard/cron-schedule.json`이 자동 갱신됨.

---

## 5. 크론 수정

- **스케줄 변경:** `CRON_SCHEDULE`의 `schedule` 필드 + `tick()` 내 시간 조건 수정
- **방식 변경:** `method` 필드 + 함수 본문 수정
- **응답 모드 변경:** `response` 필드 + 함수 내 tmux_inject/log_status 조건 수정

수정 후 반드시 스케줄러 재시작.

---

## 6. 크론 삭제

`scheduler_daemon.py` 안에서 3곳 정리 + TRIGGERS.md에서 해당 트리거 삭제 + 재시작:

1. `CRON_SCHEDULE`에서 해당 항목 제거
2. 크론 함수 삭제
3. `tick()` 내 해당 조건 블록 삭제
4. TRIGGERS.md에서 해당 트리거 삭제
5. 스케줄러 재시작

---

## 7. 크론 상태 조회

**현재 상태:** `dashboard/cron-status.json` 읽기
**최근 이력:** `dashboard/cron-log.json` 읽기
**텍스트 로그:** `tail -30 memory/cron.log`

### 상태 코드

- `ok` — 정상 완료 (silent 작업)
- `sent` — 주입 완료 (새 데이터 전달됨)
- `no_new` — 새 데이터 없음 (정상)
- `no_bus` — 버스 정보 없음 (정상)
- `error` — 에러 발생
- `session_expired` — 외부 서비스 세션 만료
- `skipped_holiday` — 휴일이라 스킵

---

## 8. tmux 주입 패턴

모든 주입은 `Cron :` 접두사가 붙는다. 메인 세션은 이 접두사로 크론 주입과 유저 메시지를 구분.

```
Cron : CRON_NAME: 내용
```

예시:
```
Cron : MORNING_BRIEFING: 10:00 회의 / 14:00 미팅
Cron : BUS_ALERT: 38번 3분(08:23)
Cron : REDDIT_READY:
Cron : EVENING_ALERT:
```

---

## 9. 크론 피어에서 마이그레이션

> 이전에 Claude Code 내장 크론 (`CronCreate`/`CronList`) + 피어 메시징 (`list_peers` → `send_message`)으로 크론을 관리했다면, Python 데몬으로 이전을 추천한다.

### 왜 이전하는가

- 내장 크론은 **세션이 끊기면 사라짐** — 재등록 필요
- 피어 메시징은 **피어 ID가 세션마다 바뀜** — 찾기 로직 필요
- Python 데몬은 **LaunchAgent로 항상 살아있음** — 세션 독립적
- tmux 주입은 **세션명만 알면 됨** — ID 변경 무관

### 이전 절차

1. `CronList`로 현재 등록된 크론 확인
2. 각 크론의 프롬프트/스케줄을 `scheduler_daemon.py`에 옮기기
3. 피어 알림(`send_message`) → `tmux_inject()` 또는 Python 직접 실행으로 교체
4. 테스트 후 `CronDelete`로 내장 크론 삭제
5. 크론 세션이 별도로 있었다면 종료

---

## 10. 에러 트러블슈팅

**"command not found" / "No such file or directory" 에러:**
LaunchAgent 환경은 PATH가 제한적. **모든 외부 명령은 반드시 절대경로**로 사용:
- Python: `/opt/homebrew/bin/python3`
- Claude: `~/.npm-global/bin/claude` (경로 확인: `which claude`)
- tmux: `/opt/homebrew/bin/tmux` (경로 확인: `which tmux`)
- 기타 CLI 도구: `which 명령어`로 확인 후 절대경로 사용

> **주의:** `run_script()`, `claude_run()`, `tmux_inject()`는 내부적으로 절대경로 상수를 쓰지만, `subprocess.run()`을 직접 호출할 때는 절대경로를 빠뜨리기 쉽다. 새 크론에서 외부 CLI를 직접 호출하면 반드시 절대경로인지 확인할 것.

**스케줄러가 안 도는 것 같을 때:**
```bash
pgrep -f scheduler_daemon  # PID 확인
tail -10 memory/scheduler.log  # 최근 로그
cat dashboard/cron-status.json  # 마지막 실행 시간 확인
```

**tmux 주입이 안 될 때:**
```bash
tmux ls  # 메인 세션 존재 확인
tmux send-keys -t my-bot-main "test" Enter  # 수동 테스트
```
