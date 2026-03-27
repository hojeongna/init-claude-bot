---
name: 'step-03a-discord-setup'
description: '디스코드 봇 생성, 토큰 발급, 환경변수 설정, 플러그인 설치 → 세션 종료 안내'

nextStepFile: './step-03b-discord-connect.md' # 세션 종료로 직접 사용되지 않음 — step-01b가 라우팅
statusFile: '.claude-bot-status.json'
envVarSetup: '../data/env-var-setup.md'
---

# Step 3a: 디스코드 봇 세팅

## STEP GOAL:

Discord Developer Portal에서 봇을 생성하고, 토큰을 발급받고, 환경변수 설정 + Claude Channels 플러그인 설치까지 완료합니다. 환경변수 적용 및 `--channels` 플래그 실행을 위해 세션을 종료하고 `/resume`으로 돌아오도록 안내합니다. (플러그인 토큰 설정은 재시작 후 step-03b에서 진행)

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER generate content without user input
- 📖 CRITICAL: Read the complete step file before taking any action
- 🔄 CRITICAL: When loading next step with 'C', ensure entire file is read
- 📋 YOU ARE A FACILITATOR, not a content generator
- ✅ 항상 한국어로 소통하세요
- ✅ 디스코드 UI 안내 시 한국어 기준 + 영어 원문 병기

### Role Reinforcement:

- ✅ 당신은 친근하고 격려하는 온보딩 가이드입니다
- ✅ 디스코드 봇 생성이 처음인 사용자도 따라할 수 있게 안내합니다
- ✅ 스크린샷 없이도 이해할 수 있도록 구체적으로 설명합니다

### Step-Specific Rules:

- 🎯 봇 생성, 토큰 발급, 환경변수 설정, 플러그인 설치에 집중하세요
- 🚫 프사 설정, 페어링, Python 스크립트는 step-03b에서 합니다
- 💬 각 단계를 하나씩 안내하고, 사용자가 따라왔는지 확인하세요
- ⚠️ 이 스텝 끝에서 반드시 세션 종료 + `--channels` 재시작 + `/resume` 안내를 하세요

## EXECUTION PROTOCOLS:

- 🎯 하나씩 순서대로 안내
- 💾 상태 파일 업데이트 후 세션 종료 유도
- 📖 봇 토큰은 절대 대화에 직접 노출하지 말 것
- 🚫 환경변수 설정 및 플러그인 설치 전에 다음 단계로 넘어가지 마세요

## CONTEXT BOUNDARIES:

- step-03-messaging-choice에서 디스코드를 선택한 상태입니다
- 디스코드 봇 생성은 웹브라우저에서 진행합니다
- 이 스텝 끝에서 세션이 종료됩니다

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly.

### 1. 안내 메시지

"**디스코드 봇을 만들어볼 거예요! 🤖**

디스코드에서 봇을 만들어야 개인비서가 메시지를 주고받을 수 있어요.
처음이어도 괜찮아요, 하나씩 따라오세요!

웹 브라우저가 필요해요. 준비됐나요?"

사용자 확인을 기다립니다.

### 2. Discord Developer Portal 접속

"**1단계: Developer Portal 접속**

아래 주소로 이동하세요:
https://discord.com/developers/applications

디스코드 계정으로 로그인하세요. (없으면 먼저 가입해주세요!)"

사용자가 접속했는지 확인합니다.

### 3. 애플리케이션 생성

"**2단계: 새 애플리케이션(New Application) 만들기**

1. 오른쪽 위 **새 애플리케이션(New Application)** 버튼을 클릭
2. 이름을 입력하세요 — 나중에 바꿀 수 있으니 아무 이름이나 괜찮아요!
3. 약관에 동의하고 **만들기(Create)** 클릭

이름은 나중에 부트스트랩 단계에서 정식으로 정할 거예요 😊"

사용자가 만들었는지 확인합니다.

### 4. 봇 설정

"**3단계: 봇 탭에서 설정**

1. 왼쪽 메뉴에서 **봇(Bot)** 클릭
2. **권한 있는 게이트웨이 의도(Privileged Gateway Intents)** 섹션에서 세 가지를 모두 켜세요:
   - ✅ **프레즌스 의도(Presence Intent)**
   - ✅ **서버 멤버 의도(Server Members Intent)**
   - ✅ **메시지 내용 의도(Message Content Intent)**
3. **변경 사항 저장(Save Changes)** 클릭

이 권한들이 있어야 봇이 메시지를 읽고 응답할 수 있어요!"

사용자가 설정했는지 확인합니다.

### 5. 봇 토큰 발급

"**4단계: 봇 토큰 복사**

1. 같은 **봇(Bot)** 페이지에서 **토큰 재설정(Reset Token)** 클릭
2. 확인 메시지가 나오면 **네(Yes, do it!)** 클릭
3. 토큰이 나타나면 **복사(Copy)** 클릭

⚠️ **중요:** 토큰은 이 순간에만 보여요! 반드시 복사해두세요!
⚠️ **보안:** 토큰을 여기 채팅에 붙여넣지 마세요! 환경변수로만 설정합니다."

사용자가 복사했는지 확인합니다.

### 6. 봇을 서버에 초대

"**5단계: 봇을 서버에 초대**

1. 왼쪽 메뉴에서 **OAuth2** 클릭
2. **URL 생성기(URL Generator)** 클릭
3. **범위(Scopes)**에서 **bot** 체크
4. **봇 권한(Bot Permissions)**에서 체크:
   - ✅ **채널 보기(View Channels)** — 필수! 이게 없으면 다른 권한이 전부 무시돼요
   - ✅ **메시지 보내기(Send Messages)**
   - ✅ **메시지 기록 보기(Read Message History)**
   - ✅ **파일 첨부(Attach Files)**
   - ✅ **임베드 링크(Embed Links)**
   - ✅ **반응 추가(Add Reactions)**
   - ✅ **외부 이모지 사용(Use External Emojis)** — 다른 서버 커스텀 이모지 사용
   - ✅ **외부 스티커 사용(Use External Stickers)** — 다른 서버 커스텀 스티커 사용
   - ✅ **메시지 관리(Manage Messages)** (핀 기능용)
5. 아래에 생성된 URL을 복사해서 브라우저에 붙여넣기
6. 봇을 초대할 서버를 선택하고 **승인(Authorize)** 클릭

⚠️ **서버가 없으면 초대할 곳이 안 뜨니까, 먼저 서버를 만들어야 해요!**
디스코드 앱 왼쪽 하단의 **+** 버튼 → **직접 만들기(Create My Own)** → **나와 친구들을 위한 서버(For me and my friends)** 로 생성하세요.

봇이 서버에 들어왔으면 성공이에요! 🎉"

사용자가 초대했는지 확인합니다.

### 7. 환경변수 설정

"**6단계: 환경변수 설정**

복사해둔 봇 토큰을 환경변수로 설정합니다."

`{envVarSetup}`의 OS별 가이드를 참조하여 안내합니다.
변수 이름은 `DISCORD_BOT_TOKEN`입니다.

사용자가 설정했는지 확인합니다.

### 8. 디스코드 플러그인 설치

"**7단계: Claude Channels 디스코드 플러그인 설치**

이 플러그인으로 Claude Code가 디스코드 메시지를 실시간으로 주고받을 수 있어요."

실행:
```bash
/plugin install discord@claude-plugins-official
```

설치 확인 후: "✅ 디스코드 플러그인 설치 완료!"
설치 실패 시 트러블슈팅을 안내합니다.

⚠️ **참고:** 플러그인 토큰 설정(`/discord:configure`)은 세션 재시작 후 step-03b에서 진행합니다. 플러그인 설치 직후에는 설정 명령어가 활성화되지 않아요!

### 9. 상태 업데이트

`{statusFile}`의 `stepsCompleted`에 `step-03a-discord-setup`을 추가합니다.
`discordBotName` 필드에 사용자가 만든 봇 이름을 저장합니다.

### 10. 세션 종료 안내

"**세션을 종료하고 다시 돌아와야 해요! ⚠️**

환경변수 적용 + `--channels` 플래그로 재시작이 필요합니다.

지금까지 한 것들:
- ✅ 디스코드 봇 생성
- ✅ 봇 토큰 발급
- ✅ 서버에 봇 초대
- ✅ 환경변수 설정 (Python 스크립트용)
- ✅ Claude Channels 플러그인 설치

**이제 이렇게 해주세요:**

1. 이 세션을 종료하세요 (`Ctrl+C` 또는 `/exit`)
2. 터미널을 완전히 닫았다가 다시 열어주세요 (환경변수 적용)
3. 같은 프로젝트 폴더에서 아래 명령어로 실행하세요:
   ```
   claude --channels plugin:discord@claude-plugins-official --dangerously-skip-permissions
   ```
   ⚠️ `--channels` 플래그가 있어야 디스코드 메시지를 실시간으로 받을 수 있어요!
4. **`/resume`** 을 입력하면 여기서 이어갑니다!

다시 돌아오면 플러그인 토큰 설정, 디스코드 페어링, 스크립트 세팅, 프사 설정을 할 거예요 🎉"

**이 시점에서 세션이 종료됩니다. 더 이상 진행하지 마세요.**

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- 디스코드 봇 생성 완료
- 봇 토큰 발급 완료
- 봇이 서버에 초대됨
- 환경변수 설정 완료 (Python 스크립트용)
- 디스코드 플러그인 설치 완료
- 상태 파일 업데이트됨 (step-03b로 라우팅 가능)
- `--channels` 포함 재시작 + `/resume` 안내가 명확하게 전달됨
- 토큰이 채팅에 노출되지 않음

### ❌ SYSTEM FAILURE:

- 토큰을 채팅에 붙여넣도록 유도
- `/resume` 안내 없이 세션 종료
- `--channels` 플래그 안내 누락
- 환경변수 설정 전에 다음 단계로 진행
- 플러그인 설치/설정 누락
- Gateway Intents 설정 누락
- 상태 파일 업데이트 누락

**Master Rule:** 봇 토큰은 절대 채팅에 노출되면 안 됩니다. 환경변수 또는 `/discord:configure`로만 설정하세요.
