---
name: 'step-03b-discord-connect'
description: '디스코드 페어링, discord.py 설치, fetch 스크립트 생성, 프사 설정'

nextStepFile: './step-04-automation.md'
statusFile: '.claude-bot-status.json'
discordScripts: '../data/discord-scripts.md'
---

# Step 3b: 디스코드 연결

## STEP GOAL:

디스코드 채널 페어링을 완료하고, discord.py와 기본 스크립트를 세팅하고, 봇 프로필 사진을 설정합니다.

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
- ✅ 설치 실패 시 대안을 제시합니다

### Step-Specific Rules:

- 🎯 플러그인/패키지 설치와 스크립트 생성에 집중하세요
- 🚫 봇 정체성 설정은 step-05에서 합니다
- 💬 각 설치가 성공했는지 확인하세요
- 🖼️ 프사 설정은 선택사항으로 안내하세요

## EXECUTION PROTOCOLS:

- 🎯 환경변수가 제대로 적용됐는지 먼저 확인
- 💾 상태 파일 업데이트
- 📖 스크립트 코드는 `{discordScripts}` 파일을 참조하세요
- 🚫 설치 실패 시 다음으로 넘어가지 말고 해결

## CONTEXT BOUNDARIES:

- step-03a에서 세션 종료 후 `claude --channels plugin:discord@claude-plugins-official`로 재시작, `/resume`으로 돌아온 상태입니다
- step-01b를 거쳐 여기로 라우팅됨
- 환경변수 DISCORD_BOT_TOKEN이 설정되어 있어야 합니다
- 디스코드 플러그인은 step-03a에서 이미 설치됨 (토큰 설정은 이 스텝에서 진행)

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly.

### 1. 환경변수 확인

"**다시 돌아오셨군요! 반가워요! 👋**

`--channels` 플래그로 실행하셨죠? 먼저 환경변수가 잘 적용됐는지 확인할게요..."

환경변수 확인:
```bash
echo $DISCORD_BOT_TOKEN | head -c 10
```

- 토큰 앞 10자가 출력되면: "✅ 환경변수 설정 확인 완료!"
- 출력이 없으면: "❌ 환경변수가 설정되지 않았어요. step-03a의 환경변수 설정을 다시 해주세요."
  → 환경변수가 확인될 때까지 진행하지 않습니다.

### 2. 플러그인에 봇 토큰 등록

"**플러그인에 봇 토큰을 등록할게요!**

step-03a에서 플러그인을 설치했는데, 재시작해야 설정 명령어가 활성화돼요.
지금은 재시작한 상태니까 바로 설정할 수 있어요!

아까 복사해둔 토큰을 `/discord:configure` 뒤에 붙여서 입력하세요:"

실행 방법:
```
/discord:configure 여기에토큰붙여넣기
```

예시 (토큰은 이런 형태예요):
```
/discord:configure MTIzNDU2Nzg5MDEyMzQ1Njc4OQ.GAbcDE.abcdefghijk...
```

⚠️ **주의:** 토큰을 일반 채팅에 그냥 붙여넣지 마세요! 반드시 `/discord:configure` 뒤에 붙여야 해요!

⚠️ **참고:** 환경변수(`DISCORD_BOT_TOKEN`)는 discord.py 스크립트용이고, `/discord:configure`는 Claude Channels 플러그인용입니다. 둘 다 필요해요!

설정이 되면 토큰 앞 6자리를 마스킹해서 보여줍니다. 확인 후: "✅ 플러그인 토큰 설정 완료!"

### 3. 디스코드 채널 페어링

"**디스코드 봇과 페어링을 할 거예요! 🔗**

이 과정으로 디스코드에서 보낸 메시지가 Claude Code까지 전달돼요."

**페어링 순서:**

1. 디스코드에서 봇에게 **DM(개인 메시지)**을 보내세요 — 아무 메시지나 괜찮아요!
2. 봇이 **페어링 코드**를 응답할 거예요. 그 코드를 복사하세요.
3. 여기서 아래 명령어를 실행하세요:
   ```
   /discord:access pair <페어링코드>
   ```
4. 페어링 성공 후, 접근 정책을 설정하세요:
   ```
   /discord:access policy allowlist
   ```

- 페어링 성공: "✅ 디스코드 페어링 완료! 이제 디스코드에서 보낸 메시지가 Claude Code로 전달돼요!"
- 실패 시: 에러를 분석하고 해결 방법을 안내합니다. `--channels` 플래그 없이 실행했을 수 있으니 확인합니다.

⚠️ **참고:** 페어링이 안 되면 `claude --channels plugin:discord@claude-plugins-official` 로 실행했는지 다시 확인하세요!

### 4. discord.py 설치

"**discord.py 패키지를 설치할게요!**

프사 변경, 상태 설정 같은 확장 기능에 필요해요."

```bash
pip install discord.py
```

### 5. 스크립트 생성

`{discordScripts}`를 읽고 두 파일을 생성합니다:

**fetch_discord.py** — 세션 시작 시 최근 채팅을 불러오는 스크립트
- `{discordScripts}`의 `fetch_discord.py` 섹션 코드를 `scripts/fetch_discord.py`로 생성
- "이 스크립트는 세션 시작 시 최근 채팅 20개를 불러와서 대화를 자연스럽게 이어가는 데 쓰여요"

**change_avatar.py** — 봇 아바타 변경 스크립트
- `{discordScripts}`의 `change_avatar.py` 섹션 코드를 `scripts/change_avatar.py`로 생성
- "이 스크립트로 나중에 봇 프사를 바꿀 수 있어요"

### 6. 프로필 사진 설정 (선택)

"**봇 프로필 사진을 설정할까요? 🖼️**

프사가 있으면 디스코드에서 봇이 더 특별해 보여요! 나중에 해도 괜찮아요.

**[Y] 지금 설정** — 이미지 파일 경로를 알려주세요
**[N] 나중에** — 부트스트랩 단계에서 봇 정체성 잡은 후에 해도 돼요"

- IF Y: 이미지 경로를 받아 `python3 scripts/change_avatar.py <경로>` 실행
- IF N: "👌 나중에 해도 돼요! `python3 scripts/change_avatar.py 이미지경로` 로 언제든 바꿀 수 있어요."

### 7. 연결 테스트

"**디스코드 연결을 테스트해볼게요!**"

```bash
python3 scripts/fetch_discord.py --limit 5
```

- 메시지가 출력되면: "✅ 디스코드 연결 성공!"
- 에러가 나면: 에러를 분석하고 해결 방법을 안내합니다.

### 8. 결과 요약

"**디스코드 연동 완료! 🎉**

- ✅ 디스코드 채널 페어링 완료
- ✅ discord.py 설치됨
- ✅ `scripts/fetch_discord.py` 생성됨
- ✅ `scripts/change_avatar.py` 생성됨
- ✅ 연결 테스트 통과

💡 **중요:** 앞으로 Claude Code를 실행할 때는 반드시 이렇게 실행하세요:
```
claude --channels plugin:discord@claude-plugins-official
```
`--channels` 플래그가 있어야 디스코드 메시지를 실시간으로 받을 수 있어요!

다음 단계에서는 드디어 봇과 첫 대화를 나눠요! 🤖✨"

### 9. 상태 업데이트

`{statusFile}`의 `stepsCompleted`에 `step-03b-discord-connect`를 추가합니다.

### 10. Present MENU OPTIONS

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

- 환경변수 확인됨
- 디스코드 채널 페어링 완료 (`/discord:access pair` + `policy allowlist`)
- discord.py 설치됨
- fetch_discord.py, change_avatar.py 생성됨
- 연결 테스트 통과
- 상태 파일 업데이트됨

### ❌ SYSTEM FAILURE:

- 환경변수 미확인 상태로 진행
- 페어링 없이 진행
- `--channels` 플래그 없이 실행된 상태에서 진행
- 설치 실패 시 무시하고 진행
- 스크립트 파일 생성 누락
- 연결 테스트 없이 진행

**Master Rule:** 환경변수가 확인되지 않으면 절대 진행하지 마세요. 페어링이 완료되지 않으면 채널 연동이 동작하지 않습니다. 디스코드 연결이 동작하는지 반드시 테스트하세요.
