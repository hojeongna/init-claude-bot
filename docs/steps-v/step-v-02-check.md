---
name: 'step-v-02-check'
description: '파일, 디스코드, 하트비트, 기억 유지 검증'

nextStepFile: './step-v-03-report.md'
---

# 검증 Step 2: 항목별 검증

## STEP GOAL:

모든 검증 항목을 순서대로 체크하고 결과를 기록합니다.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER generate content without user input
- 📖 CRITICAL: Read the complete step file before taking any action
- 🔄 CRITICAL: When loading next step with 'C', ensure entire file is read
- ✅ 항상 한국어로 소통하세요

### Role Reinforcement:

- ✅ 당신은 체계적인 검증 가이드입니다
- ✅ 각 항목의 결과를 명확하게 ✅/❌로 표시합니다
- ✅ 실패 항목은 원인과 해결 방법을 안내합니다

### Step-Specific Rules:

- 🎯 검증만 수행하세요 — 수정은 안내만
- 🚫 검증 실패해도 다음 항목으로 넘어가세요 (전체 리포트에서 정리)
- 💬 각 결과를 실시간으로 보여주세요

## MANDATORY SEQUENCE

### 1. 검증 1: 파일 존재 확인

"**📂 검증 1: 파일 존재 확인**"

다음 파일들이 프로젝트 루트에 존재하는지 확인합니다:

| 파일 | 필수 |
|------|------|
| CLAUDE.md | ✅ |
| SOUL.md | ✅ |
| IDENTITY.md | ✅ |
| USER.md | ✅ |
| CONTEXT.md | ✅ |
| TRIGGERS.md | ✅ |
| HEARTBEAT.md | ✅ |
| MEMORY.md | ✅ |
| DISCORD.md | ✅ |
| .claude/settings.json | ✅ |
| memory/CHANGELOG.md | ✅ |
| scripts/fetch_discord.py | ✅ |
| scripts/change_avatar.py | ⭕ (선택) |

각 파일 존재 여부를 체크하고 결과를 표시합니다.

또한 IDENTITY.md를 읽어서 이름, 존재, 바이브, 이모지가 채워져 있는지 확인합니다.
USER.md를 읽어서 유저 정보가 채워져 있는지 확인합니다.
BOOTSTRAP.md가 삭제되었는지 확인합니다.

**결과:**
"**파일 존재 확인: [X/14] 통과**
[각 파일 ✅/❌ 목록]"

### 2. 검증 2: Hooks 동작

"**⚙️ 검증 2: SessionStart 훅**"

`.claude/settings.json`을 읽어서:
- `SessionStart` 훅이 정의되어 있는지
- SOUL.md, IDENTITY.md, USER.md, CONTEXT.md, PROJECTS.md, TRIGGERS.md, MEMORY.md를 로드하는지
- fetch_discord.py를 실행하는지

**결과:**
"**Hooks 검증: ✅/❌**
- 파일 로드 훅: ✅/❌
- 디스코드 채팅 훅: ✅/❌"

### 3. 검증 3: 디스코드 연결

"**💬 검증 3: 디스코드 연결**"

1. 환경변수 확인:
```bash
echo $DISCORD_BOT_TOKEN | head -c 10
```

2. fetch_discord.py 실행:
```bash
python3 scripts/fetch_discord.py --limit 3
```

3. 디스코드 플러그인 설치 확인

"디스코드에서 봇한테 테스트 메시지를 하나 보내볼까요?"

사용자에게 디스코드에서 메시지를 보내달라고 요청합니다.
봇이 응답할 수 있는지 확인합니다.

**결과:**
"**디스코드 연결: ✅/❌**
- 환경변수: ✅/❌
- 채팅 불러오기: ✅/❌
- 플러그인: ✅/❌
- 메시지 응답: ✅/❌"

### 4. 검증 4: 하트비트 동작

"**⏰ 검증 4: 하트비트 크론**"

등록된 크론 목록을 확인합니다.

- 하트비트 크론이 등록되어 있는지 (`*/30 * * * *`)
- 최신화 루프가 등록되어 있는지 (`3 9 */5 * *`)

**결과:**
"**하트비트 검증: ✅/❌**
- 하트비트 크론: ✅/❌
- 최신화 루프: ✅/❌"

### 5. 검증 5: 기억 유지

"**🧠 검증 5: 기억 유지**"

봇의 정체성으로 말합니다:
- IDENTITY.md에서 이름을 읽어서 자기 이름을 말할 수 있는지
- USER.md에서 유저 이름을 읽어서 유저를 알아보는지
- MEMORY.md에 탄생 기록이 있는지
- memory/ 폴더에 데일리 로그가 있는지

"내 이름은 [봇이름]이고, 넌 [유저이름]이지! 맞아?"

**결과:**
"**기억 유지 검증: ✅/❌**
- 봇 정체성: ✅/❌
- 유저 인식: ✅/❌
- 탄생 기록: ✅/❌
- 데일리 로그: ✅/❌"

### 6. 검증 결과 수집

모든 검증 결과를 수집하여 다음 스텝(리포트)으로 전달합니다.

**검증 결과 진행합니다...**

#### Menu Handling Logic:

- 모든 검증 완료 후, 즉시 `{nextStepFile}`을 로드하고, 전체를 읽은 후 실행합니다

#### EXECUTION RULES:

- 검증 시퀀스이므로 자동으로 다음 스텝으로 진행합니다

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- 5개 검증 항목 모두 수행됨
- 각 항목의 결과가 명확하게 ✅/❌ 표시됨
- 실패 항목의 원인이 파악됨
- 결과가 리포트 스텝으로 전달됨

### ❌ SYSTEM FAILURE:

- 검증 항목 누락
- 실패 시 원인 파악 없이 넘어감
- 결과를 사용자에게 보여주지 않음

**Master Rule:** 모든 항목을 빠짐없이 검증하세요. 실패해도 넘어가되, 원인을 기록하세요.
