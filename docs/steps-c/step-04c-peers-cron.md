---
name: 'step-04c-peers-cron'
description: '크론 세션에 크론 등록 + 메인 세션에 헬스체크 등록'

nextStepFile: './step-05-bootstrap.md'
statusFile: '.claude-bot-status.json'
---

# Step 4c: 크론 등록 (Peers 모드)

## STEP GOAL:

크론 세션에 모든 크론을 등록하고, 메인 세션에는 헬스체크 크론만 등록합니다.

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

### Step-Specific Rules:

- 🎯 크론 등록에만 집중하세요
- 🚫 크론 세션의 크론은 send_message로 등록을 요청합니다
- 💬 각 크론이 왜 필요한지 설명하세요
- 🔧 메인 세션에는 헬스체크 1개만 등록합니다

## EXECUTION PROTOCOLS:

- 🎯 크론 세션에 메시지로 크론 등록 요청
- 💾 메인 세션에 헬스체크 크론 등록
- 📖 memory/cron-registry.md 초기화

## CONTEXT BOUNDARIES:

- step-04b에서 크론 세션과 통신이 확인된 상태입니다
- 크론 세션이 cron-worker로 태깅되어 실행 중입니다
- 메인 세션에서 list_peers로 크론 세션을 찾을 수 있습니다

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly.

### 1. 크론 구조 설명

"**크론을 등록할 거예요! ⏰**

Peers 모드에서는 이렇게 나뉘어요:

**크론 세션 (별도 터미널):**
- 하트비트 (30분마다) — 대화 수집 + 파일 업데이트
- 최신화 루프 (5일마다) — 전체 설정 리뷰
- 크론 스냅샷 (1일마다) — 크론 목록 백업
- 크론 재등록 (5일마다) — 만료 방지

**메인 세션 (여기):**
- 헬스체크 (1시간마다) — 크론 세션 살아있는지 확인

메인 세션은 가볍게, 무거운 건 크론 세션이 맡아요!"

### 2. 크론 세션에 등록 요청

`list_peers`로 cron-worker 피어를 찾고, `send_message`로 크론 등록을 요청합니다:

```
다음 크론들을 전부 백그라운드 서브에이전트로 등록해줘:

1. 하트비트 (*/30 * * * *)
   "Run as background agent: Read HEARTBEAT.md and execute instructions. Respond HEARTBEAT_OK if nothing to report."

2. 최신화 루프 (3 9 */5 * *)
   "Run as background agent: Review all config files (CLAUDE.md, SOUL.md, IDENTITY.md, USER.md, CONTEXT.md, PROJECTS.md, TRIGGERS.md, MEMORY.md) for outdated info. Update as needed. Log changes to memory/CHANGELOG.md. Respond REFRESH_OK."

3. 크론 스냅샷 (7 0 * * *)
   "Run as background agent: Run CronList. Update memory/cron-registry.md — overwrite the '현재 등록된 크론' section with full cron list (schedule + prompt for each). Append a snapshot entry to '스냅샷 이력' with timestamp. Respond SNAPSHOT_OK."

4. 크론 재등록 (11 9 */5 * *)
   "Run as background agent: Read memory/cron-registry.md. Delete ALL existing crons via CronDelete. Re-register every cron listed in the registry via CronCreate (including this re-registration cron itself). Update the registry with new job IDs. Respond REREGISTER_OK."

등록 완료되면 "CRON_REGISTERED" 라고 답해줘.
```

응답을 기다립니다.

**IF "CRON_REGISTERED" 수신:**
"✅ 크론 세션에 4개 크론 등록 완료!"

**IF 응답 없음 또는 에러:**
사용자에게 크론 세션 상태를 확인하도록 안내합니다.

### 3. 메인 세션 헬스체크 등록

메인 세션에 헬스체크 크론 1개를 등록합니다:

"**메인 세션에 헬스체크를 등록할게요.**

1시간마다 크론 세션이 살아있는지 확인해요.
만약 죽어있으면 알려줄게요!"

CronCreate로 등록:
```
스케줄: 0 * * * *
프롬프트: "Run as background agent: Call list_peers (scope: machine). Check if a peer with summary containing 'cron-worker' exists. If found, respond HEALTH_OK. If NOT found, notify the user: '⚠️ 크론 세션이 꺼져 있어요! 다른 터미널에서 다시 시작해주세요.'"
```

등록 후: "✅ 헬스체크 크론 등록 완료!"

### 4. 크론 레지스트리 초기화

`memory/cron-registry.md`에 등록된 크론 정보를 기록합니다.

**Peers 모드 형식:**

```markdown
# Cron Registry

_크론 모드: Peers 분리_

## 크론 세션 크론

| 이름 | 스케줄 | 위치 |
|------|--------|------|
| 하트비트 | */30 * * * * | 크론 세션 |
| 최신화 루프 | 3 9 */5 * * | 크론 세션 |
| 크론 스냅샷 | 7 0 * * * | 크론 세션 |
| 크론 재등록 | 11 9 */5 * * | 크론 세션 |

## 메인 세션 크론

| 이름 | 스케줄 | 위치 |
|------|--------|------|
| 헬스체크 | 0 * * * * | 메인 세션 |
```

### 5. 등록 결과 확인

"**크론 등록 완료! 🎉**

| 위치 | 크론 | 주기 |
|------|------|------|
| 크론 세션 | 하트비트 | 30분마다 |
| 크론 세션 | 최신화 루프 | 5일마다 |
| 크론 세션 | 크론 스냅샷 | 1일마다 |
| 크론 세션 | 크론 재등록 | 5일마다 |
| 메인 세션 | 헬스체크 | 1시간마다 |

크론 세션이 모든 무거운 작업을 맡고,
메인 세션은 크론 세션이 잘 돌아가는지만 확인해요!

다음 단계에서는 드디어 봇과 첫 대화를 나눠요! 🎊"

### 6. 상태 업데이트

`{statusFile}`의 `stepsCompleted`에 `step-04c-peers-cron`을 추가합니다.

### 7. Present MENU OPTIONS

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

- 크론 세션에 4개 크론 등록 요청 및 확인
- 메인 세션에 헬스체크 크론 등록
- memory/cron-registry.md 초기화 (Peers 모드 형식)
- 사용자가 크론 구조를 이해함
- 상태 파일 업데이트됨

### ❌ SYSTEM FAILURE:

- 크론 세션 확인 없이 크론 등록
- 메인 세션에 크론 세션용 크론을 등록
- 크론 레지스트리 초기화 누락
- 상태 파일 업데이트 누락

**Master Rule:** 크론 세션의 크론은 send_message로 요청하고, 메인 세션에는 헬스체크만 등록합니다.
