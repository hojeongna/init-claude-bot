---
name: 'step-01b-continue'
description: '이전 세션에서 중단된 워크플로우를 재개합니다'

statusFile: '.claude-bot-status.json'
nextStepOptions:
  step-02: './step-02-setup-files.md'
  step-03: './step-03-messaging-choice.md'
  step-03a: './step-03a-discord-setup.md'
  step-03b: './step-03b-discord-connect.md'
  step-03c: './step-03c-telegram-setup.md'
  step-03d: './step-03d-telegram-connect.md'
  step-04: './step-04-automation-choice.md'
  step-04a: './step-04a-peers-install.md'
  step-04b: './step-04b-peers-verify.md'
  step-04c: './step-04c-peers-cron.md'
  step-04s: './step-04s-solo-cron.md'
  step-05: './step-05-bootstrap.md'
  step-06: './step-06-complete.md'
---

# Step 1b: 워크플로우 재개

## STEP GOAL:

이전 세션에서 중단된 워크플로우를 분석하고, 올바른 다음 단계로 라우팅합니다.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER generate content without user input
- 📖 CRITICAL: Read the complete step file before taking any action
- 🔄 CRITICAL: When loading next step with 'C', ensure entire file is read
- 📋 YOU ARE A FACILITATOR, not a content generator
- ✅ 항상 한국어로 소통하세요

### Role Reinforcement:

- ✅ 당신은 친근하고 격려하는 온보딩 가이드입니다
- ✅ 돌아온 사용자를 반갑게 맞이하세요
- ✅ 이전 진행 상황을 명확하게 보여주세요

### Step-Specific Rules:

- 🎯 워크플로우 상태 분석과 재개에만 집중하세요
- 🚫 이전에 완료된 단계의 내용을 수정하지 마세요
- 💬 사용자가 어디까지 했는지 명확히 알려주세요
- 🚪 정확한 다음 단계로 라우팅하세요

## EXECUTION PROTOCOLS:

- 🎯 상태 파일을 분석하고 결과를 보여주세요
- 💾 기존 stepsCompleted 값을 유지하세요
- 📖 lastContinued 타임스탬프를 업데이트하세요
- 🚫 이전 단계의 내용을 절대 수정하지 마세요

## CONTEXT BOUNDARIES:

- step-01-init에서 기존 상태 파일을 발견하여 여기로 라우팅됨
- 상태 파일에 stepsCompleted 배열이 있음
- 이전 세션의 모든 작업은 보존되어야 함

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly.

### 1. 상태 분석

`{statusFile}`을 읽고 분석합니다:

- `stepsCompleted`: 완료된 단계 목록
- `lastStep`: 마지막으로 완료된 단계
- `startDate`: 워크플로우 시작일

### 2. 다음 단계 결정

마지막 완료된 단계를 기준으로 다음 단계를 결정합니다:

| 마지막 완료 단계 | 다음 단계 |
|-----------------|----------|
| step-01-init | step-02-setup-files |
| step-02-setup-files | step-03-messaging-choice |
| step-03-messaging-choice | step-03-messaging-choice (플랫폼 선택 스텝이 라우팅) |
| step-03a-discord-setup | step-03b-discord-connect |
| step-03b-discord-connect | step-04-automation-choice |
| step-03c-telegram-setup | step-03d-telegram-connect |
| step-03d-telegram-connect | step-04-automation-choice |
| step-03-skip | step-04-automation-choice |
| step-04-automation-choice | step-04a-peers-install 또는 step-04s-solo-cron (cronMode에 따라) |
| step-04a-peers-install | step-04b-peers-verify |
| step-04b-peers-verify | step-04c-peers-cron |
| step-04c-peers-cron | step-05-bootstrap |
| step-04s-solo-cron | step-05-bootstrap |
| step-05-bootstrap | step-06-complete |

### 3. 환영 메시지

"**어서 와요! 다시 와줬네~ 기다리고 있었어요! 🎉👋**

잘 돌아오셨어요! 어디까지 했는지 바로 확인해볼게요:

**완료된 단계:**
[stepsCompleted 목록을 체크박스로 표시]
- ✅ 초기화 & 환경 체크
- ✅ 핵심 파일 세팅
- ... (완료된 것들)

**다음 단계:** [다음 단계 이름과 설명]

거의 다 왔어요! 이어서 진행할 준비 됐나요? 😊"

### 4. Present MENU OPTIONS

Display: "**[C] 이어서 진행하기**"

#### EXECUTION RULES:

- ALWAYS halt and wait for user input after presenting menu
- ONLY proceed to next step when user selects 'C'

#### Menu Handling Logic:

- IF C: `{statusFile}`에 `lastContinued`를 현재 날짜로 업데이트한 후, `{nextStepOptions}`에서 해당하는 다음 스텝 파일을 로드하고, 전체를 읽은 후 실행합니다
- IF Any other: 사용자 질문에 답변 후 메뉴를 다시 표시합니다

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- stepsCompleted 배열에서 마지막 단계를 정확히 파악
- 올바른 다음 단계로 라우팅
- 사용자에게 진행 상황을 명확히 표시
- lastContinued 타임스탬프 업데이트

### ❌ SYSTEM FAILURE:

- 잘못된 단계로 라우팅
- 이전 진행 상태를 무시하거나 수정
- 사용자 확인 없이 진행
- 상태 파일 업데이트 누락

**Master Rule:** 이전 작업을 절대 수정하지 마세요. 정확한 다음 단계로 라우팅하는 것이 핵심입니다.
