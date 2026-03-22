---
name: 'step-03c-telegram-setup'
description: '텔레그램 봇 생성, 토큰 발급, 플러그인 설치/설정 → 세션 종료 안내'

nextStepFile: './step-03d-telegram-connect.md' # 세션 종료로 직접 사용되지 않음 — step-01b가 라우팅
statusFile: '.claude-bot-status.json'
---

# Step 3c: 텔레그램 봇 세팅

## STEP GOAL:

Telegram BotFather에서 봇을 생성하고, 토큰을 발급받고, Claude Channels 텔레그램 플러그인을 설치/설정합니다. `--channels` 플래그로 재시작을 위해 세션을 종료하고 `/resume`으로 돌아오도록 안내합니다.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER generate content without user input
- 📖 CRITICAL: Read the complete step file before taking any action
- 🔄 CRITICAL: When loading next step with 'C', ensure entire file is read
- 📋 YOU ARE A FACILITATOR, not a content generator
- ✅ 항상 한국어로 소통하세요

### Role Reinforcement:

- ✅ 당신은 친근하고 격려하는 온보딩 가이드입니다
- ✅ 텔레그램 봇 생성이 처음인 사용자도 따라할 수 있게 안내합니다
- ✅ 스크린샷 없이도 이해할 수 있도록 구체적으로 설명합니다

### Step-Specific Rules:

- 🎯 봇 생성, 토큰 발급, 플러그인 설치/설정에 집중하세요
- 🚫 페어링은 step-03d에서 합니다
- 💬 각 단계를 하나씩 안내하고, 사용자가 따라왔는지 확인하세요
- ⚠️ 이 스텝 끝에서 반드시 세션 종료 + `--channels` 재시작 + `/resume` 안내를 하세요

## EXECUTION PROTOCOLS:

- 🎯 하나씩 순서대로 안내
- 💾 상태 파일 업데이트 후 세션 종료 유도
- 📖 봇 토큰은 절대 대화에 직접 노출하지 말 것
- 🚫 플러그인 설정 전에 다음 단계로 넘어가지 마세요

## CONTEXT BOUNDARIES:

- step-03-messaging-choice에서 텔레그램을 선택한 상태입니다
- 텔레그램 앱이 필요합니다 (모바일 또는 데스크톱)
- 이 스텝 끝에서 세션이 종료됩니다

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly.

### 1. 안내 메시지

"**텔레그램 봇을 만들어볼 거예요! ✈️**

텔레그램에서 봇을 만들어야 개인비서가 메시지를 주고받을 수 있어요.
디스코드보다 간단해요! 하나씩 따라오세요!

텔레그램 앱이 필요해요. 준비됐나요?"

사용자 확인을 기다립니다.

### 2. BotFather로 봇 생성

"**1단계: BotFather에게 봇 만들어달라고 하기**

1. 텔레그램에서 **@BotFather** 를 검색하세요 (파란 체크 있는 공식 계정)
2. `/start` 를 보내세요
3. `/newbot` 을 보내세요
4. 봇의 **표시 이름(display name)**을 입력하세요 — 나중에 바꿀 수 있어요!
5. 봇의 **사용자 이름(username)**을 입력하세요 — 반드시 `bot`으로 끝나야 해요!
   - 예: `my_assistant_bot`, `coolhelper_bot`

BotFather가 토큰을 보내줄 거예요! 🎉"

사용자가 만들었는지 확인합니다.

### 3. 봇 토큰 복사

"**2단계: 봇 토큰 복사**

BotFather가 보내준 메시지에서 토큰을 복사하세요.
`123456789:ABCdefGHI...` 이런 형태의 긴 문자열이에요.

⚠️ **중요:** 토큰을 반드시 복사해두세요!
⚠️ **보안:** 토큰을 여기 채팅에 붙여넣지 마세요!"

사용자가 복사했는지 확인합니다.

### 4. 텔레그램 플러그인 설치

"**3단계: Claude Channels 텔레그램 플러그인 설치**

이 플러그인으로 Claude Code가 텔레그램 메시지를 실시간으로 주고받을 수 있어요."

실행:
```bash
/plugin install telegram@claude-plugins-official
```

설치 확인 후: "✅ 텔레그램 플러그인 설치 완료!"
설치 실패 시 트러블슈팅을 안내합니다.

### 5. 플러그인에 봇 토큰 등록

"**4단계: 플러그인에 봇 토큰 설정**

방금 설치한 플러그인에 토큰을 등록해야 해요."

실행:
```
/telegram:configure
```

이 명령어를 실행하면 토큰 입력을 안내받습니다. 안내에 따라 진행하세요.

설정 확인 후: "✅ 플러그인 토큰 설정 완료!"

### 6. 상태 업데이트

`{statusFile}`의 `stepsCompleted`에 `step-03c-telegram-setup`을 추가합니다.
`telegramBotName` 필드에 사용자가 만든 봇 이름을 저장합니다.

### 7. 세션 종료 안내

"**세션을 종료하고 다시 돌아와야 해요! ⚠️**

`--channels` 플래그로 재시작이 필요합니다.

지금까지 한 것들:
- ✅ 텔레그램 봇 생성
- ✅ 봇 토큰 발급
- ✅ Claude Channels 플러그인 설치 및 토큰 등록

**이제 이렇게 해주세요:**

1. 이 세션을 종료하세요 (`Ctrl+C` 또는 `/exit`)
2. 같은 프로젝트 폴더에서 아래 명령어로 실행하세요:
   ```
   claude --channels plugin:telegram@claude-plugins-official
   ```
   ⚠️ `--channels` 플래그가 있어야 텔레그램 메시지를 실시간으로 받을 수 있어요!
3. **`/resume`** 을 입력하면 여기서 이어갑니다!

다시 돌아오면 텔레그램 페어링과 연결 테스트를 할 거예요 🎉"

**이 시점에서 세션이 종료됩니다. 더 이상 진행하지 마세요.**

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- 텔레그램 봇 생성 완료
- 봇 토큰 발급 완료
- 텔레그램 플러그인 설치 완료
- `/telegram:configure` 토큰 등록 완료
- 상태 파일 업데이트됨 (step-03d로 라우팅 가능)
- `--channels` 포함 재시작 + `/resume` 안내가 명확하게 전달됨
- 토큰이 채팅에 노출되지 않음

### ❌ SYSTEM FAILURE:

- 토큰을 채팅에 붙여넣도록 유도
- `/resume` 안내 없이 세션 종료
- `--channels` 플래그 안내 누락
- 플러그인 설치/설정 누락
- 상태 파일 업데이트 누락

**Master Rule:** 봇 토큰은 절대 채팅에 노출되면 안 됩니다. `/telegram:configure`로만 설정하세요.
