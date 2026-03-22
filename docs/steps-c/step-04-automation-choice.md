---
name: 'step-04-automation-choice'
description: '크론 방식 선택 — Peers 분리 모드 또는 단일 세션 모드'

peersInstallStep: './step-04a-peers-install.md'
soloCronStep: './step-04s-solo-cron.md'
statusFile: '.claude-bot-status.json'
---

# Step 4: 자동화 방식 선택

## STEP GOAL:

크론(자동 반복 작업) 운영 방식을 선택합니다.

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
- 💬 각 방식의 장단점을 쉽게 설명하세요

## EXECUTION PROTOCOLS:

- 🎯 두 가지 모드의 차이를 설명
- 💾 상태 파일에 선택 기록
- 📖 선택에 따라 적절한 스텝으로 라우팅

## CONTEXT BOUNDARIES:

- step-03 (메시징 플랫폼 설정)이 완료된 상태입니다
- Bun은 step-01에서 이미 설치 확인 완료

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly.

### 1. 크론 개념 설명

"**자동화를 설정할 거예요! ⏰**

크론은 정해진 시간마다 자동으로 실행되는 작업이에요.
봇이 혼자서도 일할 수 있게 해주는 거예요!

두 가지 운영 방식 중에 골라주세요:"

### 2. 방식 비교 설명

"**[P] Peers 분리 모드 (추천!)**
- 크론 전용 세션을 별도 터미널에서 운영해요
- 메인 세션은 대화에만 집중, 크론은 따로 돌아가요
- 세션 재시작해도 크론 세션은 살아있어요
- claude-peers-mcp 설치가 필요해요

**[S] 단일 세션 모드 (간단)**
- 하나의 세션에서 크론을 전부 돌려요
- 추가 설치 없이 바로 시작 가능
- 세션을 끄면 크론도 사라져요 (재시작 시 자동 복원)
- 7일마다 크론이 만료돼서 재등록이 필요해요"

### 3. Present MENU OPTIONS

Display: "**어떤 방식으로 할까요?** [P] Peers 분리 모드 [S] 단일 세션 모드"

#### Menu Handling Logic:

- IF P: `{statusFile}` 업데이트 (cronMode: "peers"), then load, read entire file, then execute {peersInstallStep}
- IF S: `{statusFile}` 업데이트 (cronMode: "solo"), then load, read entire file, then execute {soloCronStep}
- IF Any other: 사용자 질문에 답변 후 메뉴를 다시 표시합니다

#### EXECUTION RULES:

- ALWAYS halt and wait for user input after presenting menu
- ONLY proceed when user selects 'P' or 'S'

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- 두 가지 방식의 차이를 사용자가 이해함
- 사용자가 방식을 선택함
- 상태 파일에 선택이 기록됨
- 적절한 다음 스텝으로 라우팅됨

### ❌ SYSTEM FAILURE:

- 방식 설명 없이 바로 선택 요구
- 사용자 선택 전에 크론 등록 시작
- 상태 파일 업데이트 누락

**Master Rule:** 사용자가 충분히 이해하고 선택할 수 있도록 두 방식의 차이를 명확히 설명해야 합니다.
