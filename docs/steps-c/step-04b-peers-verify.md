---
name: 'step-04b-peers-verify'
description: 'peers MCP 연결 확인 및 크론 세션 안내'

nextStepFile: './step-04c-peers-cron.md'
statusFile: '.claude-bot-status.json'
---

# Step 4b: Peers 검증

## STEP GOAL:

peers MCP 서버가 정상 작동하는지 확인하고, 크론 전용 세션을 안내합니다.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER generate content without user input
- 📖 CRITICAL: Read the complete step file before taking any action
- 🔄 CRITICAL: When loading next step with 'C', ensure entire file is read
- 📋 YOU ARE A FACILITATOR, not a content generator
- ✅ 항상 한국어로 소통하세요

### Role Reinforcement:

- ✅ 당신은 친근하고 격려하는 온보딩 가이드입니다
- ✅ 문제가 생기면 당황하지 않게 도와주세요

### Step-Specific Rules:

- 🎯 peers 연결 확인에만 집중하세요
- 🚫 크론 등록은 하지 마세요 (다음 스텝에서)
- 💬 크론 세션 띄우는 방법을 명확히 안내하세요
- 🔧 연결 실패 시 트러블슈팅 안내

## EXECUTION PROTOCOLS:

- 🎯 MCP 연결 확인 및 피어 통신 테스트
- 💾 상태 파일 업데이트
- 📖 크론 세션 안내

## CONTEXT BOUNDARIES:

- step-04a에서 peers를 설치하고 세션을 재시작한 상태입니다
- MCP 서버가 이번 세션에서 로드되어야 합니다
- 크론 세션은 아직 띄우지 않은 상태입니다

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly.

### 1. MCP 연결 확인

`list_peers` (scope: "machine")을 호출하여 peers MCP가 정상 작동하는지 확인합니다.

**IF 성공 (에러 없이 응답):**

"✅ peers MCP 서버가 정상 연결됐어요!"

자신의 요약을 설정합니다:
`set_summary("메인 세션 — 온보딩 진행 중")`

**IF 실패:**

"❌ peers MCP 서버 연결에 실패했어요.

확인해볼 것:
1. `claude mcp list`로 claude-peers가 등록되어 있는지 확인
2. 상태가 'Connected'인지 확인
3. 'Failed'면 `/mcp`로 재연결 시도

다시 시도해볼까요?"

사용자와 함께 문제를 해결한 후 재시도합니다.

### 2. 크론 세션 안내

"**이제 크론 전용 세션을 띄울 거예요! 🖥️**

다른 터미널(탭)을 열고 같은 프로젝트 폴더에서 Claude Code를 시작해주세요:

```
cd [현재 프로젝트 경로]
claude
```

시작하면 그 세션에서 이렇게 말해주세요:

> `set_summary`를 `cron-worker`로 설정해줘

준비되면 알려주세요! 제가 확인할게요."

### 3. 크론 세션 확인

사용자가 준비되었다고 하면, `list_peers` (scope: "machine")을 호출합니다.

**IF cron-worker 피어가 보임:**

"✅ 크론 세션이 보여요! 통신 테스트를 해볼게요."

`send_message`로 크론 피어에 테스트 메시지를 보냅니다:
"안녕! 크론 세션 연결 테스트야. 잘 받으면 '확인'이라고 답해줘."

응답을 기다립니다 (check_messages 또는 채널 푸시).

**IF 응답 수신:**
"✅ 크론 세션과 통신 성공! 완벽해요! 🎉"

**IF 응답 없음 (30초 후):**
"크론 세션에서 응답이 안 왔어요. 크론 세션 터미널을 확인해보세요."

**IF cron-worker 피어 안 보임:**

"크론 세션이 아직 안 보여요. 확인해주세요:
1. 다른 터미널에서 Claude Code가 실행 중인지
2. 같은 프로젝트 폴더에서 시작했는지
3. `set_summary`를 `cron-worker`로 설정했는지

다시 확인해볼까요?"

### 4. 상태 업데이트

`{statusFile}`의 `stepsCompleted`에 `step-04b-peers-verify`를 추가합니다.

### 5. Present MENU OPTIONS

Display: "**[C] 다음 단계로 진행 (크론 등록)**"

#### EXECUTION RULES:

- ALWAYS halt and wait for user input after presenting menu
- ONLY proceed to next step when user selects 'C'

#### Menu Handling Logic:

- IF C: `{statusFile}` 업데이트 후, `{nextStepFile}`을 로드하고, 전체를 읽은 후 실행합니다
- IF Any other: 사용자 질문에 답변 후 메뉴를 다시 표시합니다

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- peers MCP 연결 확인됨
- 자신의 summary 설정됨
- 크론 세션이 띄워지고 cron-worker로 태깅됨
- 두 세션 간 통신 테스트 성공
- 상태 파일 업데이트됨

### ❌ SYSTEM FAILURE:

- MCP 연결 확인 없이 진행
- 크론 세션 없이 다음 스텝 진행
- 통신 테스트 생략
- 상태 파일 업데이트 누락

**Master Rule:** 크론 세션이 확인되고 통신이 검증된 후에만 다음 스텝으로 진행해야 합니다.
