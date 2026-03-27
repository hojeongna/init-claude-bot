---
name: 'step-04-automation-choice'
description: '자동화 설정 — Python 스케줄러 데몬 설치 또는 기존 Peers 업그레이드'

daemonSetupStep: './step-04a-daemon-setup.md'
migrateStep: './step-04u-migrate.md'
statusFile: '.claude-bot-status.json'
---

# Step 4: 자동화 설정

## STEP GOAL:

Python 스케줄러 데몬을 설치해서 봇이 자율적으로 동작하게 합니다.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER generate content without user input
- 📖 CRITICAL: Read the complete step file before taking any action
- 🔄 CRITICAL: When loading next step with 'C', ensure entire file is read
- 📋 YOU ARE A FACILITATOR, not a content generator
- ✅ 항상 한국어로 소통하세요

### Role Reinforcement:

- ✅ 당신은 친근하고 격려하는 온보딩 가이드입니다
- ✅ 크론이 뭔지 모르는 사용자도 이해할 수 있게 설명합니다

### Step-Specific Rules:

- 🎯 방식 선택에만 집중하세요
- 🚫 아직 크론을 등록하지 마세요
- 💬 데몬이 왜 필요한지 쉽게 설명하세요

## EXECUTION PROTOCOLS:

- 🎯 데몬 모드 설명
- 💾 상태 파일에 선택 기록
- 📖 적절한 스텝으로 라우팅

## CONTEXT BOUNDARIES:

- step-03 (메시징 플랫폼 설정)이 완료된 상태입니다
- Bun은 step-01에서 이미 설치 확인 완료

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly.

### 1. 크론 개념 설명

"**자동화를 설정할 거예요! ⏰**

크론은 정해진 시간마다 자동으로 실행되는 작업이에요.
봇이 혼자서도 일할 수 있게 해주는 거예요!

Python 스케줄러 데몬을 설치해서 세션과 독립적으로 크론을 돌릴 거예요:
- tmux + LaunchAgent로 항상 백그라운드에서 돌아가요
- 세션을 재시작해도 크론이 살아있어요
- 크론 만료 걱정 없어요
- `/scheduler-manage` 스킬로 편하게 관리할 수 있어요"

### 2. 기존 Peers 유저 확인

"혹시 이전에 **Peers 분리 모드**로 운영하고 있었나요?
(claude-peers-mcp + 크론 세션 분리 방식)

**[N] 아니요, 처음이에요** — 새로 설치할게요!
**[U] 네, Peers에서 업그레이드할게요** — 기존 크론을 안전하게 옮겨드릴게요"

### 3. Present MENU OPTIONS

Display: "**[N] 새로 설치 [U] Peers에서 업그레이드**"

#### Menu Handling Logic:

- IF N: `{statusFile}` 업데이트 (cronMode: "daemon"), then load, read entire file, then execute {daemonSetupStep}
- IF U: `{statusFile}` 업데이트 (cronMode: "daemon", migrating: true), then load, read entire file, then execute {migrateStep}
- IF Any other: 사용자 질문에 답변 후 메뉴를 다시 표시합니다

#### EXECUTION RULES:

- ALWAYS halt and wait for user input after presenting menu
- ONLY proceed when user selects 'N' or 'U'

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- 데몬 모드의 장점을 사용자가 이해함
- 사용자가 새 설치 또는 업그레이드를 선택함
- 상태 파일에 선택이 기록됨
- 적절한 다음 스텝으로 라우팅됨

### ❌ SYSTEM FAILURE:

- 방식 설명 없이 바로 선택 요구
- 사용자 선택 전에 크론 등록 시작
- 상태 파일 업데이트 누락

**Master Rule:** 사용자가 충분히 이해하고 선택할 수 있도록 데몬 모드와 업그레이드 옵션을 명확히 설명해야 합니다.
