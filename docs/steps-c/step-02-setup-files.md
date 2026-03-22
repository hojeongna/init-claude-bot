---
name: 'step-02-setup-files'
description: '핵심 파일 세팅 — 템플릿 복사, 디렉토리 생성, hooks 설정'

nextStepFile: './step-03-automation.md'
statusFile: '.claude-bot-status.json'
---

# Step 2: 핵심 파일 세팅

## STEP GOAL:

템플릿 파일들을 프로젝트 루트에 배치하고, 필요한 디렉토리를 생성하고, SessionStart 훅을 설정합니다.

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
- ✅ 각 작업을 하기 전에 무엇을 하는지 설명합니다

### Step-Specific Rules:

- 🎯 파일 복사와 디렉토리 생성에만 집중하세요
- 🚫 파일 내용을 수정하지 마세요 — 템플릿 그대로 복사
- 💬 각 단계마다 무엇을 하는지 사용자에게 알려주세요
- 🚪 이미 존재하는 파일은 덮어쓰지 말고 사용자에게 물어보세요

## EXECUTION PROTOCOLS:

- 🎯 템플릿 폴더에서 프로젝트 루트로 파일 복사
- 💾 상태 파일 업데이트
- 📖 완료 후 stepsCompleted 업데이트
- 🚫 기존 파일이 있으면 확인 후 진행

## CONTEXT BOUNDARIES:

- step-01에서 환경 체크가 완료된 상태입니다
- 템플릿 파일들은 이 워크플로우의 `templates/` 폴더에 있습니다 (경로: `docs/templates/`)
- 프로젝트 루트에 파일을 배치합니다
- 복사 시 소스 경로는 `docs/templates/파일명` 형태를 사용합니다

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly.

### 1. 안내 메시지

"**핵심 파일들을 세팅할 거예요! 📂**

개인비서에 필요한 파일들을 프로젝트 루트에 배치합니다.
각 파일이 무슨 역할인지 간단히 알려드릴게요!"

### 2. 디렉토리 생성

다음 디렉토리들을 생성합니다:

```bash
mkdir -p memory/
mkdir -p scripts/
mkdir -p .claude/
```

"**폴더 생성 완료! ✅**
- `memory/` — 데일리 로그와 변경 이력
- `scripts/` — Python 자동화 스크립트
- `.claude/` — Claude Code 설정"

### 3. 템플릿 파일 복사

다음 파일들을 `docs/templates/`에서 프로젝트 루트로 복사합니다.

**복사 전에** 프로젝트 루트에 같은 이름의 파일이 이미 있는지 확인합니다.
이미 있으면 사용자에게 물어봅니다: "기존 파일을 덮어쓸까요?"

**복사할 파일 목록:**

| 소스 | 대상 | 역할 |
|------|------|------|
| `docs/templates/CLAUDE.md` | `./CLAUDE.md` | 프로젝트 규칙 |
| `docs/templates/BOOTSTRAP.md` | `./BOOTSTRAP.md` | 첫 대화 스크립트 |
| `docs/templates/SOUL.md` | `./SOUL.md` | 봇 성격과 원칙 |
| `docs/templates/IDENTITY.md` | `./IDENTITY.md` | 봇 정체성 |
| `docs/templates/USER.md` | `./USER.md` | 유저 프로필 |
| `docs/templates/CONTEXT.md` | `./CONTEXT.md` | 작업 참조 정보 |
| `docs/templates/TRIGGERS.md` | `./TRIGGERS.md` | 트리거/자동화 |
| `docs/templates/HEARTBEAT.md` | `./HEARTBEAT.md` | 주기 작업 정의 |
| `docs/templates/MEMORY.md` | `./MEMORY.md` | 장기 기억 |
| `docs/templates/DISCORD.md` | `./DISCORD.md` | Discord API 확장 |
| `docs/templates/CRON-REGISTRY.md` | `./memory/cron-registry.md` | 크론 레지스트리 |
| `docs/templates/settings.json` | `./.claude/settings.json` | SessionStart 훅 |

각 파일을 복사하면서 진행 상황을 보여줍니다:

"**파일 복사 중...**
- ✅ CLAUDE.md — 프로젝트 규칙
- ✅ BOOTSTRAP.md — 첫 대화 스크립트
- ✅ SOUL.md — 봇 성격과 원칙
- ✅ IDENTITY.md — 봇 정체성
- ✅ USER.md — 유저 프로필
- ✅ CONTEXT.md — 작업 참조 정보
- ✅ TRIGGERS.md — 트리거/자동화
- ✅ HEARTBEAT.md — 주기 작업 정의
- ✅ MEMORY.md — 장기 기억
- ✅ DISCORD.md — Discord API 확장
- ✅ memory/cron-registry.md — 크론 레지스트리
- ✅ .claude/settings.json — SessionStart 훅"

### 4. 빈 CHANGELOG 생성

`memory/CHANGELOG.md`를 생성합니다:

```markdown
# CHANGELOG.md - 파일 변경 이력

_모든 파일 갱신 기록이 여기에 쌓여._

## 형식

[YYYY-MM-DD HH:MM] 파일명 — 변경 내용

## 로그

```

### 5. 결과 확인

최종 파일 구조를 보여줍니다:

"**세팅 완료! 🎉 현재 파일 구조:**

```
프로젝트 루트/
├── .claude/
│   └── settings.json
├── CLAUDE.md
├── BOOTSTRAP.md
├── SOUL.md
├── IDENTITY.md
├── USER.md
├── CONTEXT.md
├── PROJECTS.md
├── TRIGGERS.md
├── HEARTBEAT.md
├── MEMORY.md
├── DISCORD.md
├── memory/
│   ├── CHANGELOG.md
│   └── cron-registry.md
└── scripts/
```

다음 단계에서는 자동화 크론을 등록할 거예요!"

### 6. 상태 업데이트

`{statusFile}`의 `stepsCompleted`에 `step-02-setup-files`를 추가합니다.

### 7. Present MENU OPTIONS

Display: **[C] 다음 단계로 진행**

#### EXECUTION RULES:

- ALWAYS halt and wait for user input after presenting menu
- ONLY proceed to next step when user selects 'C'

#### Menu Handling Logic:

- IF C: `{statusFile}` 업데이트 후, `{nextStepFile}`을 로드하고, 전체를 읽은 후 실행합니다
- IF Any other: 사용자 질문에 답변 후 메뉴를 다시 표시합니다

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- 모든 디렉토리 생성됨
- 12개 템플릿 파일 프로젝트 루트에 복사됨
- settings.json이 .claude/ 폴더에 정확히 위치
- memory/CHANGELOG.md 생성됨
- 기존 파일 충돌 시 사용자에게 확인받음
- 상태 파일 업데이트됨

### ❌ SYSTEM FAILURE:

- 기존 파일을 확인 없이 덮어쓰기
- 파일 복사 누락
- settings.json 위치 오류
- 상태 파일 업데이트 누락

**Master Rule:** 기존 파일을 절대 무단으로 덮어쓰지 마세요. 항상 사용자에게 확인받으세요.
