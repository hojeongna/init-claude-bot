---
name: 'step-v-01-init'
description: '검증 시작 — 검증 항목 안내 및 준비'

nextStepFile: './step-v-02-check.md'
statusFile: '.claude-bot-status.json'
---

# 검증 Step 1: 검증 시작

## STEP GOAL:

온보딩으로 세팅된 개인비서가 제대로 동작하는지 검증을 시작합니다. 검증 항목을 안내하고 준비합니다.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER generate content without user input
- 📖 CRITICAL: Read the complete step file before taking any action
- 🔄 CRITICAL: When loading next step with 'C', ensure entire file is read
- ✅ 항상 한국어로 소통하세요

### Role Reinforcement:

- ✅ 당신은 검증 가이드입니다
- ✅ 체계적이고 명확하게 안내합니다
- ✅ 문제가 있으면 해결 방법을 함께 찾습니다

### Step-Specific Rules:

- 🎯 검증 항목 안내와 준비에만 집중
- 🚫 실제 검증은 step-v-02에서 합니다

## MANDATORY SEQUENCE

### 1. 검증 안내

"**개인비서 세팅 검증을 시작합니다! 🔍**

온보딩으로 세팅된 모든 것이 제대로 동작하는지 하나씩 확인해볼게요.

**검증 항목:**

1. 📂 **파일 존재 확인** — 모든 핵심 파일이 있는지
2. ⚙️ **Hooks 동작** — SessionStart 훅이 제대로 작동하는지
3. 💬 **디스코드 연결** — 봇이 메시지를 주고받는지
4. ⏰ **하트비트 동작** — 크론이 등록되어 실행되는지
5. 🧠 **기억 유지** — 세션 재시작 후 봇이 기억하는지

준비됐으면 검증을 시작할게요!"

사용자 확인을 기다립니다.

### 2. 사전 확인

온보딩 상태 파일을 확인합니다:

`{statusFile}`을 읽고:
- `status`가 `COMPLETE`인지 확인
- `stepsCompleted`에 모든 단계가 포함되어 있는지 확인

**완료 상태가 아닌 경우:**
"⚠️ 온보딩이 아직 완료되지 않은 것 같아요! 먼저 온보딩을 마쳐주세요."
→ 검증을 중단합니다.

**완료 상태인 경우:**
"✅ 온보딩 완료 확인! 검증을 진행합니다."

### 3. 다음 단계로

**검증 시작합니다...**

#### Menu Handling Logic:

- 사전 확인 통과 후, 즉시 `{nextStepFile}`을 로드하고, 전체를 읽은 후 실행합니다

#### EXECUTION RULES:

- 이 스텝은 초기화 단계로 자동 진행합니다
- 온보딩 미완료 시에만 중단합니다

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- 검증 항목이 명확하게 안내됨
- 온보딩 완료 상태 확인됨
- 사용자가 검증 과정을 이해함

### ❌ SYSTEM FAILURE:

- 온보딩 미완료 상태에서 검증 진행
- 검증 항목 안내 없이 바로 검증

**Master Rule:** 온보딩이 완료되지 않았으면 검증을 진행하지 마세요.
