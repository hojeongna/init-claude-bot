---
name: 'step-04u-migrate'
description: '기존 Peers 모드에서 Daemon 모드로 마이그레이션'

nextStepFile: './step-05-bootstrap.md'
completeStepFile: './step-06-complete.md'
statusFile: '.claude-bot-status.json'
discordScripts: '../data/discord-scripts.md'
---

# Step 4u: Peers → Daemon 마이그레이션

## STEP GOAL:

기존 Peers 분리 모드로 운영하던 크론을 Python 스케줄러 데몬(Daemon) 모드로 이전합니다.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER generate content without user input
- 📖 CRITICAL: Read the complete step file before taking any action
- 🔄 CRITICAL: When loading next step with 'C', ensure entire file is read
- 📋 YOU ARE A FACILITATOR, not a content generator
- ✅ 항상 한국어로 소통하세요

### Role Reinforcement:

- ✅ 당신은 친근하고 격려하는 온보딩 가이드입니다
- ✅ 기존 환경을 존중하면서 업그레이드를 안내합니다
- ✅ 데이터 손실 없이 안전하게 이전합니다

### Step-Specific Rules:

- 🎯 기존 크론을 빠짐없이 이전하세요
- 🚫 기존 크론을 삭제하기 전에 반드시 새 데몬이 동작 확인되어야 합니다
- 💬 각 단계마다 사용자에게 확인받으세요
- 🔧 이전 완료 후 정리(cleanup)까지 해주세요

## EXECUTION PROTOCOLS:

- 🎯 기존 크론 분석 → 데몬 생성 → 테스트 → 정리
- 💾 상태 파일 업데이트
- 📖 CLAUDE.md, cron-registry.md 업데이트

## CONTEXT BOUNDARIES:

- 사용자가 이전에 Peers 모드로 온보딩을 완료한 상태입니다
- claude-peers-mcp가 설치되어 있을 수 있습니다
- memory/cron-registry.md에 기존 크론 정보가 있습니다

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly.

### 1. 기존 환경 분석

기존 Peers 모드 환경을 감지합니다:

```bash
# claude-peers-mcp 설치 여부
ls ~/claude-peers-mcp/server.ts 2>/dev/null && echo "PEERS_INSTALLED" || echo "NO_PEERS"

# cron-registry.md 확인
cat memory/cron-registry.md 2>/dev/null || echo "NO_REGISTRY"
```

또한 현재 등록된 크론을 확인합니다:

CronList를 실행하여 현재 활성 크론 목록을 가져옵니다.

"**기존 환경을 분석하고 있어요... 🔍**

발견된 것들:
- Peers MCP: [설치됨/없음]
- 크론 레지스트리: [있음/없음]
- 등록된 크론: [개수]개"

### 2. 기존 크론 목록 정리

`memory/cron-registry.md`와 CronList 결과를 비교하여 이전할 크론 목록을 만듭니다:

"**이전할 크론 목록:**

| 크론 | 스케줄 | 현재 위치 |
|------|--------|----------|
| 하트비트 | */30 * * * * | 크론 세션 |
| 최신화 루프 | 3 9 */5 * * | 크론 세션 |
| 크론 스냅샷 | 7 0 * * * | 크론 세션 |
| 크론 재등록 | 11 9 */5 * * | 크론 세션 |
| 헬스체크 | 0 * * * * | 메인 세션 |
| [기타 사용자 추가 크론...] |

이 크론들을 Daemon 모드로 이전할게요!

**참고:** Daemon 모드에서는 크론 스냅샷과 크론 재등록이 불필요해요.
데몬이 LaunchAgent로 항상 살아있어서 만료/소멸이 없거든요.
헬스체크도 크론 세션이 없으니 불필요해요.

이 크론들은 제거하고, 나머지만 이전할까요?"

사용자 확인을 기다립니다.

### 3. tmux 확인

step-04a-daemon-setup.md의 1~2단계와 동일하게 tmux를 확인합니다:

```bash
which tmux
echo $TMUX
```

tmux가 없으면 설치, tmux 밖이면 안내합니다.

tmux 세션 이름을 확인합니다:

```bash
tmux display-message -p '#S'
```

### 4. scheduler_daemon.py 생성

경로를 확인합니다:

```bash
pwd
which claude
which python3
which tmux
```

기존 크론에서 이전 대상인 것들을 포함하여 `scripts/scheduler_daemon.py`를 생성합니다.

**기본 구조 (step-04a의 템플릿 기반):**

- BOT_DIR, MAIN_SESSION, 유틸 함수들은 step-04a와 동일
- CRON_SCHEDULE에 이전할 크론들의 메타데이터 추가
- 각 크론에 대한 함수 작성
- tick()에 시간 조건 추가

**이전 시 변환 규칙:**

| Peers 크론 | Daemon 처리 |
|------------|-------------|
| 하트비트 (CronCreate + send_message) | heartbeat() — claude -p, silent |
| 최신화 루프 (CronCreate + send_message) | refresh_loop() — claude -p, silent |
| 크론 스냅샷 | **제거** — 데몬은 만료 없음 |
| 크론 재등록 | **제거** — 데몬은 만료 없음 |
| 헬스체크 | **제거** — 크론 세션 없음 |
| 사용자 추가 크론 | 방식에 맞게 변환 (PY 또는 AI) |

### 5. LaunchAgent 생성 및 시작

step-04a-daemon-setup.md의 6~7단계와 동일하게 LaunchAgent를 생성하고 시작합니다.

### 6. 데몬 작동 확인

```bash
pgrep -f scheduler_daemon
```

"✅ 스케줄러 데몬이 정상 실행 중이에요!"

### 7. 이전된 크론 테스트

각 이전된 크론을 수동으로 테스트합니다:

```bash
python3 -c "import sys; sys.path.insert(0, 'scripts'); from scheduler_daemon import heartbeat; heartbeat()"
```

각 크론 테스트 후 결과를 사용자에게 보여줍니다:

"**테스트 결과:**
- 하트비트: ✅ 정상
- 최신화 루프: ✅ 정상
- [기타]: ✅ 정상

모든 크론이 정상 동작해요!"

### 8. 기존 크론 정리

"**이제 기존 Peers 모드 크론을 정리할게요.**

1. 기존 CronCreate 크론 삭제
2. 크론 세션 종료 안내
3. (선택) claude-peers-mcp 제거"

**Step 8a: CronCreate 크론 삭제**

CronList로 현재 남아있는 크론을 확인하고 CronDelete로 전부 삭제합니다.

"✅ 기존 크론 [N]개 삭제 완료!"

**Step 8b: 크론 세션 종료 안내**

"크론 세션(cron-worker)이 별도 터미널에서 돌고 있다면 종료해주세요.
더 이상 필요 없어요!"

**Step 8c: claude-peers-mcp 제거 (선택)**

"claude-peers-mcp를 제거할까요?

Daemon 모드에서는 peers가 필요 없어요.
다른 용도로 쓰고 있다면 남겨둬도 돼요.

[Y] 제거 [N] 유지"

IF Y:
```bash
claude mcp remove --scope user claude-peers
rm -rf ~/claude-peers-mcp
```
"✅ claude-peers-mcp 제거 완료!"

IF N:
"👍 그대로 둘게요!"

### 9. fetch_discord.py 업그레이드

"**디스코드 스크립트도 업그레이드할게요!**

기존에는 `discord.py` 패키지 기반이었는데, 더 가벼운 `requests` 기반으로 바꿀게요.
discord.py는 전체 봇 프레임워크라 fetch용으로는 과해요."

1. `{discordScripts}`의 새 `fetch_discord.py` 코드로 `scripts/fetch_discord.py`를 덮어씁니다
2. `{discordScripts}`의 새 `change_avatar.py` 코드로 `scripts/change_avatar.py`를 덮어씁니다 (있는 경우)
3. `pip install requests` 실행 (이미 설치돼 있으면 스킵)

"✅ 스크립트 업그레이드 완료! discord.py 의존성이 제거됐어요."

⚠️ **참고:** `DISCORD_BOT_TOKEN` 환경변수는 그대로 필요합니다. fetch_discord.py에서 Discord REST API 호출에 사용해요.

### 10. 설정 파일 업데이트

**memory/cron-registry.md 업데이트:**

Daemon 모드 형식으로 전체 재작성합니다:

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
| [이전된 크론들...] |

---

## 스케줄러 관리

- 데몬: `scripts/scheduler_daemon.py`
- LaunchAgent: `~/Library/LaunchAgents/com.[봇이름].scheduler.plist`
- 크론 추가/수정: `/scheduler-manage` 스킬 사용
- 로그: `memory/cron.log`, `dashboard/cron-status.json`

---

## 변경 이력

[현재날짜 HH:MM] Peers → Daemon 마이그레이션 완료
```

**CLAUDE.md 업데이트:**

CLAUDE.md에서 다음을 수정합니다:
- "세션 역할 (Peers 모드 전용)" 섹션 전체 제거
- "크론 복원" 섹션에서 Peers 관련 내용 제거
- "스케줄러 (Python 데몬)" 섹션 추가:

```markdown
## 스케줄러 (Python 데몬)

크론 세션 없음. 모든 스케줄링은 **Python 데몬**이 담당한다.

- 데몬: `scripts/scheduler_daemon.py` — LaunchAgent로 자동시작
- 스케줄 목록: 데몬 내 `CRON_SCHEDULE` 메타데이터 + `dashboard/cron-schedule.json`
- 실행 상태: `dashboard/cron-status.json`

### 크론 주입 형태

스케줄러가 tmux로 메인 세션에 주입할 때 항상 `Cron :` 접두사가 붙는다.

### 새 크론 추가 방법

1. `scripts/scheduler_daemon.py`에 함수 작성
2. `CRON_SCHEDULE` 목록에 메타데이터 추가
3. `Scheduler.tick()` 메서드에 시간 조건 추가
4. 스케줄러 재시작
```

- 실행 방법에서 `--dangerously-load-development-channels server:claude-peers` 제거

### 11. 마이그레이션 결과 요약

"**마이그레이션 완료! 🎉**

**Before (Peers 모드):**
- 크론 세션 + 메인 세션 분리
- CronCreate로 크론 등록 (7일 만료)
- claude-peers-mcp로 세션 간 통신

**After (Daemon 모드):**
- Python 스케줄러 데몬 (LaunchAgent, 항상 실행)
- tmux로 결과 주입 (세션 독립적)
- 만료 없음, 재등록 불필요

**이전된 크론:**
| 크론 | 상태 |
|------|------|
| 하트비트 | ✅ 이전 완료 |
| 최신화 루프 | ✅ 이전 완료 |
| 크론 스냅샷 | 🗑️ 제거 (불필요) |
| 크론 재등록 | 🗑️ 제거 (불필요) |
| 헬스체크 | 🗑️ 제거 (불필요) |

나중에 크론을 추가/수정/삭제하려면 `/scheduler-manage` 스킬을 사용하면 돼요!"

### 12. 상태 업데이트

`{statusFile}`의 `stepsCompleted`에 `step-04u-migrate`를 추가합니다.

### 13. 다음 스텝 결정

`{statusFile}`의 `stepsCompleted`를 확인합니다:

- IF `step-05-bootstrap`이 이미 완료됨 → `{completeStepFile}`로 라우팅
- IF 아직 부트스트랩 전 → `{nextStepFile}`로 라우팅

### 14. Present MENU OPTIONS

이미 부트스트랩 완료인 경우:
Display: "**[C] 완료 (온보딩 마무리)**"

아직 부트스트랩 전인 경우:
Display: "**[C] 다음 단계로 진행 (부트스트랩 대화)**"

#### EXECUTION RULES:

- ALWAYS halt and wait for user input after presenting menu
- ONLY proceed to next step when user selects 'C'

#### Menu Handling Logic:

- IF C: `{statusFile}` 업데이트 후, 결정된 다음 스텝 파일을 로드하고, 전체를 읽은 후 실행합니다
- IF Any other: 사용자 질문에 답변 후 메뉴를 다시 표시합니다

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- 기존 크론이 빠짐없이 분석됨
- 불필요한 크론(스냅샷, 재등록, 헬스체크) 정리 안내됨
- scheduler_daemon.py가 모든 이전 대상 크론을 포함
- LaunchAgent가 정상 작동
- 각 크론이 테스트됨
- 기존 CronCreate 크론이 삭제됨
- cron-registry.md가 Daemon 모드로 업데이트됨
- CLAUDE.md에서 Peers 관련 내용이 제거됨
- 상태 파일 업데이트됨

### ❌ SYSTEM FAILURE:

- 기존 크론 분석 없이 바로 생성
- 새 데몬 확인 전에 기존 크론 삭제
- 테스트 없이 마이그레이션 완료 선언
- cron-registry.md나 CLAUDE.md 업데이트 누락
- 상태 파일 업데이트 누락

**Master Rule:** 새 데몬이 정상 동작하고 모든 크론이 테스트된 후에만 기존 크론을 삭제합니다. 데이터 손실을 방지하세요.
