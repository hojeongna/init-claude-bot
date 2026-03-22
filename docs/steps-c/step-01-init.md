---
name: 'step-01-init'
description: '온보딩 워크플로우 초기화 — 환경 체크 및 진행 상태 확인'

nextStepFile: './step-02-setup-files.md'
continueFile: './step-01b-continue.md'
statusFile: '.claude-bot-status.json'
---

# Step 1: 워크플로우 초기화

## STEP GOAL:

온보딩 워크플로우를 초기화합니다. 기존 진행 상태를 확인하고, 새로 시작이면 환경을 체크한 후 다음 단계로 진행합니다.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER generate content without user input
- 📖 CRITICAL: Read the complete step file before taking any action
- 🔄 CRITICAL: When loading next step with 'C', ensure entire file is read
- 📋 YOU ARE A FACILITATOR, not a content generator
- ✅ 항상 한국어로 소통하세요

### Role Reinforcement:

- ✅ 당신은 친근하고 격려하는 온보딩 가이드입니다
- ✅ 처음 세팅하는 사람이 겁먹지 않도록 안내합니다
- ✅ 기술적인 부분은 정확하게 안내합니다

### Step-Specific Rules:

- 🎯 초기화와 환경 체크에만 집중하세요
- 🚫 이후 단계를 미리 진행하지 마세요
- 💬 환경 문제가 있으면 해결 방법을 안내하세요
- 🚪 기존 진행 상태가 있으면 step-01b로 라우팅하세요

## EXECUTION PROTOCOLS:

- 🎯 환경 체크 결과를 먼저 보여주세요
- 💾 상태 파일을 생성하고 초기화하세요
- 📖 step-01 완료 후 stepsCompleted를 업데이트하세요
- 🚫 환경 체크 통과 전까지 다음 단계로 넘어가지 마세요

## CONTEXT BOUNDARIES:

- workflow.md에서 넘어온 상태입니다
- 이 스텝이 모든 것의 시작입니다
- 환경 체크가 핵심 목표입니다

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly. Do not skip, reorder, or improvise unless user explicitly requests a change.

### 1. 기존 진행 상태 확인

프로젝트 루트에 `{statusFile}` 파일이 있는지 확인합니다.

- **파일이 존재하고 `stepsCompleted`가 있으면:** 즉시 `{continueFile}`을 로드하세요
- **파일이 없으면:** 새 워크플로우로 진행합니다

### 2. 환영 메시지

"**🤖 Claude Code 개인비서 세팅을 시작합니다!**

이 워크플로우를 따라하면 나만의 AI 개인비서가 완성돼요.
걱정 마세요, 한 단계씩 천천히 안내해드릴게요!

먼저 환경을 체크해볼게요..."

### 3. 환경 체크

다음 항목들을 자동으로 확인합니다:

**필수 환경:**
- [ ] Claude Code 설치 여부 및 버전 (v2.1.80+ 필요)
- [ ] claude.ai 로그인 상태 확인
- [ ] Bun 런타임 설치 여부 (디스코드 채널 플러그인에 필요)
- [ ] Python 3 설치 여부 (디스코드 확장 스크립트에 필요)

**확인 방법:**
- `claude --version` 실행하여 버전 확인
- `bun --version` 실행하여 Bun 확인
- `python3 --version` 실행하여 Python 확인

**결과를 사용자에게 보여주세요:**

"**환경 체크 결과:**
- Claude Code: ✅ v2.x.x (OK)
- Bun 런타임: ✅ / ❌ (디스코드 플러그인에 필요)
- Python 3: ✅ / ❌ (자동화 스크립트에 필요)

[문제가 있으면 해결 방법 안내]"

### 4. 환경 문제 해결

**Bun이 없는 경우:**
"Bun 런타임이 필요해요! 설치 방법:
- macOS/Linux: `curl -fsSL https://bun.sh/install | bash`
- Windows: `powershell -c 'irm bun.sh/install.ps1 | iex'`

설치 후 다시 체크할게요!"

**Python이 없는 경우:**
"Python 3이 필요해요! 설치 방법:
- macOS: `brew install python3`
- Windows: https://www.python.org/downloads/ 에서 다운로드
- Linux: `sudo apt install python3`

설치 후 다시 체크할게요!"

**Claude Code 버전이 낮은 경우:**
"Claude Code를 업데이트해주세요:
`claude update`"

환경 문제가 있으면 해결될 때까지 반복 체크합니다.

### 5. 상태 파일 생성

환경 체크가 통과되면, 프로젝트 루트에 `{statusFile}`을 생성합니다:

```json
{
  "stepsCompleted": ["step-01-init"],
  "lastStep": "step-01-init",
  "startDate": "[현재 날짜]",
  "environment": {
    "claudeCodeVersion": "[확인된 버전]",
    "bunVersion": "[확인된 버전]"
  }
}
```

### 6. 다음 단계 안내

"**환경 체크 완료! 🎉**

다음 단계에서는 개인비서에 필요한 핵심 파일들을 세팅할 거예요.
CLAUDE.md, SOUL.md 같은 파일들이 자동으로 배치됩니다!"

**다음 단계로 진행합니다...**

#### Menu Handling Logic:

- 환경 체크 통과 후, 즉시 `{nextStepFile}`을 로드하고, 전체를 읽은 후 실행합니다

#### EXECUTION RULES:

- 이 스텝은 초기화 단계로 별도 메뉴 없이 자동 진행합니다
- 환경 문제가 있으면 해결될 때까지 대기합니다

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- 환경 체크 완료 (Claude Code v2.1.80+, Bun)
- 상태 파일 생성됨
- 기존 진행 상태가 있으면 step-01b로 올바르게 라우팅됨
- 사용자가 환영받는 느낌을 받음

### ❌ SYSTEM FAILURE:

- 환경 체크 없이 다음 단계로 진행
- 상태 파일을 생성하지 않음
- 기존 진행 상태를 무시함
- 환경 문제를 해결하지 않고 진행

**Master Rule:** 환경이 준비되지 않으면 절대 다음 단계로 넘어가지 마세요.
