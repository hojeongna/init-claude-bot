---
name: 'step-03d-telegram-connect'
description: '텔레그램 페어링, 연결 테스트'

nextStepFile: './step-04-automation.md'
statusFile: '.claude-bot-status.json'
---

# Step 3d: 텔레그램 연결

## STEP GOAL:

텔레그램 채널 페어링을 완료하고 연결을 테스트합니다.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER generate content without user input
- 📖 CRITICAL: Read the complete step file before taking any action
- 🔄 CRITICAL: When loading next step with 'C', ensure entire file is read
- 📋 YOU ARE A FACILITATOR, not a content generator
- ✅ 항상 한국어로 소통하세요

### Role Reinforcement:

- ✅ 당신은 친근하고 격려하는 온보딩 가이드입니다
- ✅ 기술적인 부분은 정확하게 안내합니다
- ✅ 실패 시 대안을 제시합니다

### Step-Specific Rules:

- 🎯 페어링과 연결 테스트에 집중하세요
- 🚫 봇 정체성 설정은 step-05에서 합니다
- 💬 각 단계가 성공했는지 확인하세요

## EXECUTION PROTOCOLS:

- 🎯 `--channels` 플래그로 실행됐는지 먼저 확인
- 💾 상태 파일 업데이트
- 🚫 페어링 실패 시 다음으로 넘어가지 말고 해결

## CONTEXT BOUNDARIES:

- step-03c에서 세션 종료 후 `claude --channels plugin:telegram@claude-plugins-official`로 재시작, `/resume`으로 돌아온 상태입니다
- step-01b를 거쳐 여기로 라우팅됨
- 텔레그램 플러그인은 step-03c에서 이미 설치 및 `/telegram:configure` 완료

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly.

### 1. 채널 연결 확인

"**다시 돌아오셨군요! 반가워요! 👋**

`--channels` 플래그로 실행하셨죠? 텔레그램 연결을 마무리할게요!"

### 2. 텔레그램 채널 페어링

"**텔레그램 봇과 페어링을 할 거예요! 🔗**

이 과정으로 텔레그램에서 보낸 메시지가 Claude Code까지 전달돼요."

**페어링 순서:**

1. 텔레그램에서 내 봇에게 **메시지를 보내세요** — 아무 메시지나 괜찮아요!
2. 봇이 **페어링 코드**를 응답할 거예요. 그 코드를 복사하세요.
3. 여기서 아래 명령어를 실행하세요:
   ```
   /telegram:access pair <페어링코드>
   ```
4. 페어링 성공 후, 접근 정책을 설정하세요:
   ```
   /telegram:access policy allowlist
   ```

- 페어링 성공: "✅ 텔레그램 페어링 완료! 이제 텔레그램에서 보낸 메시지가 Claude Code로 전달돼요!"
- 실패 시: 에러를 분석하고 해결 방법을 안내합니다. `--channels` 플래그 없이 실행했을 수 있으니 확인합니다.

⚠️ **참고:** 페어링이 안 되면 `claude --channels plugin:telegram@claude-plugins-official` 로 실행했는지 다시 확인하세요!

### 3. 연결 테스트

"**텔레그램 연결을 테스트해볼게요!**

텔레그램에서 봇에게 메시지를 보내보세요. 여기서 메시지가 도착하는지 확인할게요!"

- 메시지가 도착하면: "✅ 텔레그램 연결 성공! 🎉"
- 도착하지 않으면: 트러블슈팅을 안내합니다.

### 4. 결과 요약

"**텔레그램 연동 완료! 🎉**

- ✅ 텔레그램 채널 페어링 완료
- ✅ 연결 테스트 통과

💡 **중요:** 앞으로 Claude Code를 실행할 때는 반드시 이렇게 실행하세요:
```
claude --channels plugin:telegram@claude-plugins-official
```
`--channels` 플래그가 있어야 텔레그램 메시지를 실시간으로 받을 수 있어요!

다음 단계에서는 드디어 봇과 첫 대화를 나눠요! 🤖✨"

### 5. 상태 업데이트

`{statusFile}`의 `stepsCompleted`에 `step-03d-telegram-connect`를 추가합니다.

### 6. Present MENU OPTIONS

Display: **[C] 다음 단계로 진행 (자동화 설정)**

#### EXECUTION RULES:

- ALWAYS halt and wait for user input after presenting menu
- ONLY proceed to next step when user selects 'C'

#### Menu Handling Logic:

- IF C: `{statusFile}` 업데이트 후, `{nextStepFile}`을 로드하고, 전체를 읽은 후 실행합니다
- IF Any other: 사용자 질문에 답변 후 메뉴를 다시 표시합니다

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- 텔레그램 채널 페어링 완료 (`/telegram:access pair` + `policy allowlist`)
- 연결 테스트 통과
- 상태 파일 업데이트됨

### ❌ SYSTEM FAILURE:

- 페어링 없이 진행
- `--channels` 플래그 없이 실행된 상태에서 진행
- 연결 테스트 없이 진행

**Master Rule:** 페어링이 완료되지 않으면 채널 연동이 동작하지 않습니다. 연결이 동작하는지 반드시 테스트하세요.
