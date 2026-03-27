---
name: 'step-04b-daemon-cron'
description: '선택적 크론 등록 — 최신화 루프, 리마인더, 커스텀 크론'

nextStepFile: './step-05-bootstrap.md'
statusFile: '.claude-bot-status.json'
---

# Step 4b: 크론 선택 (Daemon 모드)

## STEP GOAL:

하트비트 외 추가 크론을 사용자가 선택하여 등록합니다. 모든 크론은 선택적입니다.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER generate content without user input
- 📖 CRITICAL: Read the complete step file before taking any action
- 🔄 CRITICAL: When loading next step with 'C', ensure entire file is read
- 📋 YOU ARE A FACILITATOR, not a content generator
- ✅ 항상 한국어로 소통하세요

### Role Reinforcement:

- ✅ 당신은 친근하고 격려하는 온보딩 가이드입니다
- ✅ 크론이 뭔지 모르는 사용자도 이해할 수 있게 설명합니다
- ✅ 각 크론이 왜 필요한지 설명합니다

### Step-Specific Rules:

- 🎯 추가 크론 선택과 등록에만 집중하세요
- 🚫 하트비트는 이미 등록되어 있으므로 다시 하지 마세요
- 💬 선택지를 명확히 보여주고, 하나도 안 골라도 괜찮다고 안내하세요
- 🔧 선택한 크론마다 scheduler_daemon.py에 추가합니다

## EXECUTION PROTOCOLS:

- 🎯 크론 메뉴 표시 후 사용자 선택 반영
- 💾 scheduler_daemon.py 업데이트 + 데몬 재시작
- 📖 memory/cron-registry.md 초기화

## CONTEXT BOUNDARIES:

- step-04a에서 데몬이 설치되고 하트비트가 등록된 상태입니다
- 데몬이 LaunchAgent로 실행 중입니다
- tmux 세션 안에서 Claude가 돌고 있습니다

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly.

### 1. 현재 상태 확인

"**하트비트는 이미 돌고 있어요! ✅**

30분마다 대화를 수집하고 파일을 업데이트해요.

추가로 설정할 수 있는 크론들이 있어요.
하나도 안 골라도 괜찮아요! 나중에 언제든 추가할 수 있어요."

### 2. 선택적 크론 메뉴

"**추가 크론 옵션:**

**[1] 최신화 루프**
- 5일마다 전체 설정 파일을 리뷰해요
- CLAUDE.md, SOUL.md, IDENTITY.md 등 모든 파일을 확인
- 오래되거나 틀린 정보가 있으면 업데이트
- 일종의 자기 점검 시간!

**[2] 리마인더**
- Claude Code 내장 크론(CronCreate)으로 1회성/반복 리마인더를 설정할 수 있어요
- '내일 1시에 XX 알려줘' 같은 간단한 알림
- 세션 종속적 — 세션이 꺼지면 사라져요
- 영구 리마인더가 필요하면 데몬에 크론으로 등록

**[3] 커스텀 크론**
- 원하는 자동화를 직접 만들어요!
- 예: 웹사이트 모니터링, 매일 뉴스 수집, API 상태 체크 등
- 어떤 걸 하고 싶은지 말해주시면 같이 만들어요

원하는 번호를 골라주세요 (여러 개 가능, 예: 1,3).
넘어가려면 **[S] 스킵** 하세요."

### 3. 선택 처리

#### IF [1] 최신화 루프 선택:

`scripts/scheduler_daemon.py`에 다음을 추가:

**(a) CRON_SCHEDULE에 메타데이터 추가:**

```python
{"key": "refresh", "name": "최신화 루프", "schedule": "30 4 */5 * *", "method": "claude -p", "response": "silent", "desc": "설정 파일 리뷰 · 업데이트"},
```

**(b) 크론 함수 추가:**

```python
def refresh_loop():
    """5일마다 — 설정 파일 리뷰 및 업데이트"""
    try:
        claude_run(
            "설정 파일들(SOUL.md, USER.md, CONTEXT.md, TRIGGERS.md 등) 전체 리뷰하고 "
            "outdated 내용 업데이트. memory/CHANGELOG.md에 기록. REFRESH_OK로 응답.",
            timeout=300
        )
        log_status("refresh_loop", "ok")
    except Exception as e:
        log_status("refresh_loop", "error", str(e))
```

**(c) tick()에 조건 추가:**

```python
# ── 최신화 루프: 5일마다 04:30 ──
if h == 4 and m == 30 and dom % 5 == 0:
    self.run_once_per_minute("refresh", refresh_loop)
```

#### IF [2] 리마인더 선택:

리마인더는 데몬이 아닌 Claude Code 내장 크론(CronCreate)으로 처리한다고 설명:

"**리마인더는 CronCreate를 사용해요!**

데몬에 따로 등록할 필요 없이, 봇한테 말하면 돼요:
- '내일 1시에 회의 알려줘' → 1회성 리마인더
- '매일 9시에 출근 알려줘' → 반복 리마인더

세션이 켜져 있는 동안 동작해요.
나중에 봇한테 자유롭게 요청하면 됩니다!"

**별도 파일 수정 없음.**

#### IF [3] 커스텀 크론 선택:

사용자에게 어떤 자동화를 원하는지 물어봅니다:

"**어떤 자동화를 만들고 싶어요?**

예시:
- 특정 웹사이트에 새 글 올라오면 알려줘
- 매일 아침에 날씨/일정 알려줘
- 특정 API를 주기적으로 체크해줘
- 매일 자정에 날짜 변경 알려줘

자유롭게 말해주세요!"

사용자가 설명하면:
1. 실행 방식 결정 (PY / AI)
2. 응답 모드 결정 (silent / conditional / notify)
3. 스케줄 결정 (분 시 일 월 요일)
4. Python 스크립트 필요하면 `scripts/` 폴더에 생성
5. `scheduler_daemon.py`에 3곳 추가 (CRON_SCHEDULE, 함수, tick)
6. 필요시 TRIGGERS.md에 트리거 등록

#### IF [S] 스킵:

"👍 하트비트만으로도 충분해요! 나중에 추가하고 싶으면 `/scheduler-manage` 스킬을 사용하면 돼요."

### 4. 데몬 재시작 (크론 추가 시)

크론이 추가된 경우에만 실행:

```bash
launchctl unload ~/Library/LaunchAgents/com.[봇이름].scheduler.plist
launchctl load ~/Library/LaunchAgents/com.[봇이름].scheduler.plist
```

재시작 후 확인:

```bash
pgrep -f scheduler_daemon
```

"✅ 데몬이 재시작됐어요! 새 크론이 적용됐습니다."

### 5. 테스트 (크론 추가 시)

추가된 크론을 수동으로 테스트합니다:

```bash
python3 -c "import sys; sys.path.insert(0, 'scripts'); from scheduler_daemon import [함수명]; [함수명]()"
```

테스트 결과를 사용자에게 보여주고 확인받습니다.

### 6. 크론 레지스트리 초기화

`memory/cron-registry.md`에 등록된 크론 정보를 기록합니다.

**Daemon 모드 형식:**

```markdown
# Cron Registry

_크론이 추가/수정/삭제될 때마다 이 파일을 업데이트할 것._

## 크론 모드

**모드:** daemon

---

## 등록된 크론

| 이름 | 스케줄 | 방식 | 응답 | 설명 |
|------|--------|------|------|------|
| 하트비트 | 4,34 * * * * | claude -p | silent | 대화 분석 · 파일 업데이트 |
| [추가된 크론들...] |

---

## 스케줄러 관리

- 데몬: `scripts/scheduler_daemon.py`
- LaunchAgent: `~/Library/LaunchAgents/com.[봇이름].scheduler.plist`
- 크론 추가/수정: `/scheduler-manage` 스킬 사용
- 로그: `memory/cron.log`, `dashboard/cron-status.json`

---

## 변경 이력

[현재날짜 HH:MM] 초기 등록 — 하트비트 [+ 추가된 크론들]
```

### 7. 등록 결과 확인

등록된 모든 크론을 요약합니다:

"**등록된 크론 ✅**

| 크론 | 주기 | 방식 |
|------|------|------|
| 하트비트 | 30분마다 | AI (claude -p) |
| [추가된 크론들...] |

스케줄러 데몬이 LaunchAgent로 항상 돌고 있어서,
세션을 재시작해도 크론은 계속 동작해요!

나중에 크론을 추가/수정/삭제하려면 `/scheduler-manage` 스킬을 사용하면 돼요!

다음 단계에서는 드디어 봇과 첫 대화를 나눠요!"

### 8. 상태 업데이트

`{statusFile}`의 `stepsCompleted`에 `step-04b-daemon-cron`을 추가합니다.

### 9. Present MENU OPTIONS

Display: "**[C] 다음 단계로 진행 (부트스트랩 대화)**"

#### EXECUTION RULES:

- ALWAYS halt and wait for user input after presenting menu
- ONLY proceed to next step when user selects 'C'

#### Menu Handling Logic:

- IF C: `{statusFile}` 업데이트 후, `{nextStepFile}`을 로드하고, 전체를 읽은 후 실행합니다
- IF Any other: 사용자 질문에 답변 후 메뉴를 다시 표시합니다

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- 크론 메뉴가 사용자에게 표시됨
- 사용자 선택이 반영됨 (스킵 포함)
- 선택한 크론이 scheduler_daemon.py에 추가됨
- 데몬이 재시작되고 정상 작동 확인됨
- memory/cron-registry.md 초기화됨
- 사용자가 결과를 확인함
- 상태 파일 업데이트됨

### ❌ SYSTEM FAILURE:

- 하트비트를 다시 등록하거나 수정함
- 사용자 선택 없이 크론을 등록함
- 데몬 재시작 누락
- 크론 레지스트리 초기화 누락
- 상태 파일 업데이트 누락

**Master Rule:** 추가 크론은 전부 선택적입니다. 사용자가 스킵해도 하트비트만으로 충분합니다. 강요하지 마세요.
