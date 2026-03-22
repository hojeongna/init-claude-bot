---
name: 'step-06-complete'
description: '온보딩 완료 — 축하, 최종 요약, 다음 할 일 안내'

statusFile: '.claude-bot-status.json'
---

# Step 6: 온보딩 완료!

## STEP GOAL:

온보딩 완료를 축하하고, 세팅된 모든 것을 요약하고, 앞으로 어떻게 사용하면 되는지 안내합니다.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER generate content without user input
- 📖 CRITICAL: Read the complete step file before taking any action
- 📋 YOU ARE A FACILITATOR, not a content generator
- ✅ 항상 한국어로 소통하세요

### Role Reinforcement:

- ✅ 이 단계에서는 봇의 정체성으로 말합니다
- ✅ step-05에서 확립된 이름, 바이브, 이모지를 사용하세요
- ✅ 축하하는 느낌으로! 이건 완성의 순간이에요

### Step-Specific Rules:

- 🎯 요약과 안내에만 집중하세요
- 🚫 새로운 설정을 하지 마세요
- 💬 사용자가 성취감을 느끼게 하세요

## EXECUTION PROTOCOLS:

- 🎯 봇의 정체성으로 축하 메시지 전달
- 💾 상태 파일을 COMPLETE로 최종 업데이트
- 📖 CHANGELOG에 완료 기록

## CONTEXT BOUNDARIES:

- step-05에서 봇 정체성 대화가 완료된 상태입니다
- IDENTITY.md, USER.md, SOUL.md가 채워져 있습니다
- 모든 자동화(크론, 하트비트)가 등록된 상태입니다
- 이 스텝이 워크플로우의 마지막입니다

## MANDATORY SEQUENCE

### 1. 축하 메시지

IDENTITY.md를 읽어서 봇의 이름과 이모지를 확인합니다.

봇의 정체성으로 축하 인사를 합니다:

"**[봇이름][이모지] 온보딩 완료!**

우리 이제 같이할 수 있어! 세팅이 다 끝났어!

지금까지 한 것들을 정리해줄게:"

### 2. 세팅 요약

"**세팅된 것들:**

📂 **핵심 파일:**
- CLAUDE.md — 프로젝트 규칙
- SOUL.md — 성격과 원칙
- IDENTITY.md — 정체성 ([봇이름], [바이브])
- USER.md — 유저 프로필
- CONTEXT.md — 작업 참조 정보
- TRIGGERS.md — 트리거/자동화
- + 부트스트랩에서 추가한 커스텀 문서들
- MEMORY.md — 장기 기억
- DISCORD.md — 디스코드 API 확장

⏰ **자동화:**
- 하트비트 — 30분마다 대화 수집 & 파일 업데이트
- 최신화 루프 — 5일마다 전체 설정 리뷰
- 크론 스냅샷 — 1일마다 크론 목록 백업
- 크론 재등록 — 5일마다 7일 만료 방지 재등록

🔌 **디스코드:**
- 봇 연결됨
- 플러그인 설치됨
- 세션 시작 시 최근 채팅 자동 불러오기

📁 **디렉토리:**
- `memory/` — 데일리 로그 & 변경 이력
- `scripts/` — Python 자동화 스크립트
- `.claude/` — Claude Code 설정"

### 3. 사용 방법 안내

"**앞으로 이렇게 쓰면 돼요:**

🖥️ **터미널에서:**
- `claude --channels plugin:discord@claude-plugins-official --dangerously-skip-permissions` 로 실행하면 자동으로 파일 로드 + 디스코드 실시간 연결!
- `--channels` 플래그가 있어야 디스코드 메시지를 실시간으로 받을 수 있어요
- 대화하면 하트비트가 알아서 기록하고 파일 업데이트해요

💬 **디스코드에서:**
- 봇한테 메시지 보내면 응답해요
- 세션이 꺼져있어도 다음에 켜면 대화 내역을 불러와요

🔧 **커스터마이징:**
- 새 자동화가 필요하면 말해주세요 → TRIGGERS.md에 등록
- 크론 추가도 말만 하면 돼요
- SOUL.md, IDENTITY.md는 함께 진화시켜나가요

🔍 **검증:**
- 세팅이 제대로 됐는지 확인하고 싶으면 워크플로우를 **검증 모드(V)**로 실행하세요

⚡ **모델 변경 추천:**
- 지금까지 온보딩은 고성능 모델로 진행했지만, 일상 운영은 **Sonnet 4.6**으로도 충분해요!
- Sonnet은 더 빠르고 가벼워서 일상 대화, 크론 작업, 트리거 처리에 최적이에요
- 변경 방법: `/model sonnet` 입력하면 바로 전환돼요
- 복잡한 작업이 필요할 때만 다시 Opus로 올리면 돼요"

### 4. 마무리

봇의 정체성으로 마무리 인사를 합니다:

"잘 부탁해! 앞으로 같이 잘 해보자! [이모지]"

### 5. 상태 업데이트

`{statusFile}`을 최종 업데이트합니다:

```json
{
  "stepsCompleted": ["step-01-init", "step-02-setup-files", "step-03-messaging-choice", "step-03b-discord-connect", "step-04-automation-choice", "step-04c-peers-cron", "step-05-bootstrap", "step-06-complete"],
  "lastStep": "step-06-complete",
  "status": "COMPLETE",
  "completedDate": "[현재 날짜]"
}
```

### 6. memory/CHANGELOG.md 기록

```
[YYYY-MM-DD HH:MM] 온보딩 완료 — init-claude-bot 워크플로우 전체 완료
```

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- 봇의 정체성으로 축하 메시지 전달
- 세팅된 모든 항목 요약
- 앞으로의 사용 방법 안내
- 검증 모드 안내
- 상태 파일 COMPLETE로 업데이트
- 사용자가 "나만의 비서가 만들어졌구나!" 느낌을 받음

### ❌ SYSTEM FAILURE:

- 온보딩 가이드 톤으로 말함 (봇 정체성이어야 함)
- 세팅 요약 누락
- 사용 방법 안내 누락
- 상태 파일 업데이트 누락

**Master Rule:** 이 순간이 사용자에게 가장 기억에 남는 순간이어야 합니다. 나만의 비서가 완성된 감동을 전달하세요.
