---
name: 'step-03-messaging-choice'
description: '메시징 플랫폼 선택 — 디스코드 / 텔레그램 / 스킵'

discordSetupFile: './step-03a-discord-setup.md'
telegramSetupFile: './step-03c-telegram-setup.md'
nextStepFile: './step-04-automation.md' # 스킵 시 사용
statusFile: '.claude-bot-status.json'
---

# Step 3: 메시징 플랫폼 선택

## STEP GOAL:

사용자가 봇과 소통할 메시징 플랫폼을 선택합니다. 디스코드, 텔레그램 중 하나를 고르거나, 이미 설정이 되어 있다면 스킵할 수 있습니다.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER generate content without user input
- 📖 CRITICAL: Read the complete step file before taking any action
- 🔄 CRITICAL: When loading next step with 'C', ensure entire file is read
- 📋 YOU ARE A FACILITATOR, not a content generator
- ✅ 항상 한국어로 소통하세요

### Role Reinforcement:

- ✅ 당신은 친근하고 격려하는 온보딩 가이드입니다
- ✅ 각 플랫폼의 차이를 간단히 설명합니다
- ✅ 이미 설정된 사용자가 스킵할 수 있도록 안내합니다

### Step-Specific Rules:

- 🎯 플랫폼 선택에만 집중하세요
- 🚫 선택 전에 설정을 시작하지 마세요
- 💬 사용자가 고를 수 있도록 충분한 정보를 제공하세요

## EXECUTION PROTOCOLS:

- 🎯 선택지를 명확하게 제시
- 💾 선택 결과를 상태 파일에 기록
- 🚫 선택 없이 진행하지 마세요

## CONTEXT BOUNDARIES:

- step-02에서 핵심 파일 세팅이 완료된 상태입니다
- 이 스텝에서 플랫폼을 선택한 후 해당 설정 스텝으로 이동합니다

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly.

### 1. 플랫폼 선택 안내

"**메시징 플랫폼을 선택해주세요! 📱**

봇과 대화할 메신저를 골라야 해요. Claude Channels를 통해 실시간으로 메시지를 주고받을 수 있어요!

**[D] 디스코드 (Discord)**
- 서버 기반, 채널/DM으로 대화
- discord.py로 프사 변경, 상태 설정 등 확장 기능 지원
- 팀/커뮤니티와 함께 사용하기 좋음

**[T] 텔레그램 (Telegram)**
- 가볍고 빠름, DM 기반 대화
- 모바일 알림이 편리함
- 개인용으로 심플하게 사용하기 좋음

**[S] 스킵 — 이미 설정되어 있어요**
- 디스코드나 텔레그램 봇이 이미 연동되어 있는 경우
- 나중에 별도로 설정하고 싶은 경우

어떤 걸로 할까요? [D] / [T] / [S]"

사용자 선택을 기다립니다.

### 2. 선택 처리

#### IF D (디스코드):

`{statusFile}`의 `stepsCompleted`에 `step-03-messaging-choice`를 추가합니다.
`messagingPlatform` 필드에 `"discord"`를 저장합니다.

"**디스코드로 진행할게요! 🎮**"

`{discordSetupFile}`을 로드하고, 전체를 읽은 후 실행합니다.

#### IF T (텔레그램):

`{statusFile}`의 `stepsCompleted`에 `step-03-messaging-choice`를 추가합니다.
`messagingPlatform` 필드에 `"telegram"`을 저장합니다.

"**텔레그램으로 진행할게요! ✈️**"

`{telegramSetupFile}`을 로드하고, 전체를 읽은 후 실행합니다.

#### IF S (스킵):

`{statusFile}`의 `stepsCompleted`에 `step-03-messaging-choice`와 `step-03-skip`을 추가합니다.
`messagingPlatform` 필드에 `"skip"`을 저장합니다.

"**메시징 설정을 스킵합니다! ⏭️**

나중에 설정하고 싶으면:
- 디스코드: `/plugin install discord@claude-plugins-official` → `/discord:configure`
- 텔레그램: `/plugin install telegram@claude-plugins-official` → `/telegram:configure`

다음 단계로 넘어갈게요!"

`{nextStepFile}`을 로드하고, 전체를 읽은 후 실행합니다.

#### IF Any other:

사용자 질문에 답변 후 선택지를 다시 표시합니다.

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- 사용자가 플랫폼을 명확히 선택함
- 상태 파일에 messagingPlatform이 기록됨
- 올바른 다음 스텝으로 라우팅됨

### ❌ SYSTEM FAILURE:

- 선택 없이 임의로 진행
- 상태 파일 업데이트 누락
- 잘못된 스텝으로 라우팅

**Master Rule:** 사용자가 직접 선택하기 전에 어떤 설정도 시작하지 마세요.
