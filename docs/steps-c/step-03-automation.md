---
name: 'step-03-automation'
description: '기본 크론 등록 — 하트비트와 최신화 루프'

nextStepFile: './step-04a-discord-setup.md'
statusFile: '.claude-bot-status.json'
---

# Step 3: 자동화 설정

## STEP GOAL:

기본 크론(하트비트 30분, 최신화 루프 5일)을 등록하여 봇이 자율적으로 동작할 수 있게 합니다.

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

- 🎯 크론 등록에만 집중하세요
- 🚫 크론 외 다른 설정을 하지 마세요
- 💬 크론 개념을 쉽게 설명하세요
- 🚪 분 오프셋으로 충돌 방지 적용

## EXECUTION PROTOCOLS:

- 🎯 크론을 백그라운드 서브에이전트로 등록
- 💾 상태 파일 업데이트
- 📖 완료 후 stepsCompleted 업데이트
- 🚫 기존 크론과 충돌하지 않도록 확인

## CONTEXT BOUNDARIES:

- step-02에서 파일 세팅이 완료된 상태입니다
- HEARTBEAT.md가 프로젝트 루트에 존재합니다
- CLAUDE.md에 크론 규칙이 정의되어 있습니다

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly.

### 1. 크론 개념 설명

"**자동화를 설정할 거예요! ⏰**

크론은 정해진 시간마다 자동으로 실행되는 작업이에요.
봇이 혼자서도 일할 수 있게 해주는 거예요!

두 가지 기본 크론을 등록할게요:"

### 2. 하트비트 크론 설명 및 등록

"**1. 하트비트 (30분마다)**

30분마다 봇이 자동으로:
- 디스코드 최근 대화를 수집하고
- 기록할 만한 것을 데일리 로그에 저장하고
- 필요하면 각 파일(USER.md, SOUL.md 등)을 업데이트해요

유저한테는 아무 알림도 안 가요. 조용히 동작합니다!"

`/loop` 또는 CronCreate를 사용하여 등록:

```
스케줄: */30 * * * *
프롬프트: "Run as background agent: Read HEARTBEAT.md and execute instructions. Respond HEARTBEAT_OK if nothing to report."
```

등록 후 확인: "✅ 하트비트 크론 등록 완료!"

### 3. 최신화 루프 설명 및 등록

"**2. 최신화 루프 (5일마다)**

5일마다 봇이 전체 설정 파일을 리뷰해요:
- CLAUDE.md, SOUL.md, IDENTITY.md 등 모든 파일을 확인
- 오래되거나 틀린 정보가 있으면 업데이트
- 변경 사항은 memory/CHANGELOG.md에 기록

일종의 자기 점검 시간이에요!"

`/loop` 또는 CronCreate를 사용하여 등록:

```
스케줄: 3 9 */5 * *
프롬프트: "Run as background agent: Review all config files (CLAUDE.md, SOUL.md, IDENTITY.md, USER.md, CONTEXT.md, PROJECTS.md, TRIGGERS.md, MEMORY.md) for outdated info. Update as needed. Log changes to memory/CHANGELOG.md. Respond REFRESH_OK."
```

**참고:** 분 오프셋 3을 줘서 하트비트와 겹치지 않게 합니다.

등록 후 확인: "✅ 최신화 루프 등록 완료!"

### 4. 크론 스냅샷 설명 및 등록

"**3. 크론 스냅샷 (1일마다)**

매일 한번 봇이 현재 등록된 크론 목록을 백업해요:
- CronList로 전체 크론 확인
- memory/cron-registry.md에 전체 목록 저장
- 변경 사항이 있으면 변경 이력도 기록

클코를 껐다 키면 크론이 전부 사라지거든요.
이 백업이 있어야 다시 복원할 수 있어요!"

`/loop` 또는 CronCreate를 사용하여 등록:

```
스케줄: 7 0 * * *
프롬프트: "Run as background agent: Run CronList. Update memory/cron-registry.md — overwrite the '## 현재 등록된 크론' section with full cron list (schedule + prompt for each). Append a snapshot entry to '## 스냅샷 이력' with timestamp. Respond SNAPSHOT_OK."
```

**참고:** 분 오프셋 7을 줘서 다른 크론과 겹치지 않게 합니다.

등록 후 확인: "✅ 크론 스냅샷 등록 완료!"

### 5. 크론 재등록 설명 및 등록

"**4. 크론 재등록 (5일마다)**

크론은 7일 후 자동으로 만료돼요.
그래서 5일마다 전체 크론을 삭제하고 새로 등록해요:
- memory/cron-registry.md에서 등록할 크론 목록을 읽고
- CronDelete로 기존 크론 전부 삭제
- CronCreate로 같은 설정 그대로 재등록
- 자기 자신(크론 재등록)도 포함!

이러면 크론이 만료되는 일이 없어요!"

`/loop` 또는 CronCreate를 사용하여 등록:

```
스케줄: 11 9 */5 * *
프롬프트: "Run as background agent: Read memory/cron-registry.md. Delete ALL existing crons via CronDelete. Re-register every cron listed in the registry via CronCreate (including this re-registration cron itself). Update the registry with new job IDs. Respond REREGISTER_OK."
```

**참고:** 분 오프셋 11을 줘서 최신화 루프(:03)와 겹치지 않게 합니다.

등록 후 확인: "✅ 크론 재등록 등록 완료!"

### 6. 크론 레지스트리 초기화

등록된 4개 크론 정보를 `memory/cron-registry.md`에 기록합니다.

각 크론의 **Job ID**, **스케줄**, **프롬프트**를 전부 기록할 것.

### 7. 크론 등록 확인

등록된 크론 목록을 보여줍니다:

"**등록된 크론 ✅**

| 크론 | 주기 | 스케줄 |
|------|------|--------|
| 하트비트 | 30분마다 | `*/30 * * * *` |
| 최신화 루프 | 5일마다 오전 9:03 | `3 9 */5 * *` |
| 크론 스냅샷 | 1일마다 자정 0:07 | `7 0 * * *` |
| 크론 재등록 | 5일마다 오전 9:11 | `11 9 */5 * *` |

크론은 7일 후 자동 만료되지만, 재등록 크론이 5일마다 갱신해줘요!
클코를 재시작해도 크론 레지스트리에서 자동 복원돼요!

나중에 크론을 추가하고 싶으면 봇한테 말하면 돼요!
다음 단계에서는 디스코드를 연동할 거예요!"

### 8. 상태 업데이트

`{statusFile}`의 `stepsCompleted`에 `step-03-automation`을 추가합니다.

### 9. Present MENU OPTIONS

Display: **[C] 다음 단계로 진행 (디스코드 연동)**

#### EXECUTION RULES:

- ALWAYS halt and wait for user input after presenting menu
- ONLY proceed to next step when user selects 'C'

#### Menu Handling Logic:

- IF C: `{statusFile}` 업데이트 후, `{nextStepFile}`을 로드하고, 전체를 읽은 후 실행합니다
- IF Any other: 사용자 질문에 답변 후 메뉴를 다시 표시합니다

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- 하트비트 크론 등록됨 (30분마다)
- 최신화 루프 등록됨 (5일마다)
- 크론 스냅샷 등록됨 (1일마다)
- 크론 재등록 등록됨 (5일마다)
- memory/cron-registry.md 초기화됨
- 분 오프셋 적용되어 충돌 방지됨
- 사용자가 크론의 역할을 이해함
- 상태 파일 업데이트됨

### ❌ SYSTEM FAILURE:

- 크론을 포그라운드로 등록 (백그라운드 서브에이전트여야 함)
- 분 오프셋 없이 같은 시간에 등록
- 크론 개념 설명 없이 바로 등록
- 크론 레지스트리(memory/cron-registry.md) 초기화 누락
- 상태 파일 업데이트 누락

**Master Rule:** 크론은 반드시 백그라운드 서브에이전트로 실행되어야 합니다. 모든 크론은 등록 즉시 memory/cron-registry.md에 기록되어야 합니다.
