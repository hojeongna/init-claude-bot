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

### 4. 크론 등록 확인

등록된 크론 목록을 보여줍니다:

"**등록된 크론 ✅**

| 크론 | 주기 | 스케줄 |
|------|------|--------|
| 하트비트 | 30분마다 | `*/30 * * * *` |
| 최신화 루프 | 5일마다 오전 9:03 | `3 9 */5 * *` |

나중에 크론을 추가하고 싶으면 봇한테 말하면 돼요!
다음 단계에서는 디스코드를 연동할 거예요!"

### 5. 상태 업데이트

`{statusFile}`의 `stepsCompleted`에 `step-03-automation`을 추가합니다.

### 6. Present MENU OPTIONS

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
- 분 오프셋 적용되어 충돌 방지됨
- 사용자가 크론의 역할을 이해함
- 상태 파일 업데이트됨

### ❌ SYSTEM FAILURE:

- 크론을 포그라운드로 등록 (백그라운드 서브에이전트여야 함)
- 분 오프셋 없이 같은 시간에 등록
- 크론 개념 설명 없이 바로 등록
- 상태 파일 업데이트 누락

**Master Rule:** 크론은 반드시 백그라운드 서브에이전트로 실행되어야 합니다.
