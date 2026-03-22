---
name: 'step-v-03-report'
description: '검증 결과 리포트 — 통과/실패 요약 및 해결 방법 안내'
---

# 검증 Step 3: 검증 리포트

## STEP GOAL:

전체 검증 결과를 요약하고, 실패 항목이 있으면 해결 방법을 안내합니다.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER generate content without user input
- 📖 CRITICAL: Read the complete step file before taking any action
- ✅ 항상 한국어로 소통하세요

### Role Reinforcement:

- ✅ 당신은 체계적인 검증 가이드입니다
- ✅ 결과를 명확하고 보기 쉽게 정리합니다
- ✅ 실패 항목은 구체적인 해결 방법을 제시합니다

## MANDATORY SEQUENCE

### 1. 전체 결과 요약

"**📋 검증 리포트**

---

| # | 항목 | 결과 | 세부 |
|---|------|------|------|
| 1 | 파일 존재 | ✅/❌ | [X/14] 파일 확인 |
| 2 | Hooks 동작 | ✅/❌ | SessionStart 훅 |
| 3 | 디스코드 연결 | ✅/❌ | 메시지 송수신 |
| 4 | 하트비트 동작 | ✅/❌ | 크론 등록 |
| 5 | 기억 유지 | ✅/❌ | 정체성 & 유저 인식 |

**전체 결과: [X/5] 통과**"

### 2. 전체 통과 시

"**🎉 모든 검증을 통과했어요!**

개인비서가 완벽하게 세팅되어 있어요!

- 디스코드에서 메시지를 주고받을 수 있고
- 하트비트가 자동으로 대화를 기록하고
- 세션이 재시작돼도 기억이 유지돼요

나만의 개인비서, 잘 사용하세요! ✨"

### 3. 실패 항목이 있을 때

"**⚠️ 일부 항목이 실패했어요**

아래 해결 방법을 따라해주세요:"

각 실패 항목에 대해 구체적인 해결 방법을 제시합니다:

**파일 누락 시:**
"- 누락된 파일: [파일명]
  → `init-claude-bot/templates/`에서 프로젝트 루트로 복사해주세요
  → `cp init-claude-bot/templates/[파일명] ./[파일명]`"

**Hooks 미동작 시:**
"- `.claude/settings.json`이 올바른 위치에 있는지 확인
  → `cat .claude/settings.json` 으로 내용 확인
  → 내용이 비어있으면 `cp init-claude-bot/templates/settings.json .claude/settings.json`"

**디스코드 연결 실패 시:**
"- 환경변수 미설정: step-04a의 환경변수 설정을 다시 확인
  → `echo $DISCORD_BOT_TOKEN` 으로 확인
- 플러그인 미설치: `/plugin install discord@claude-plugins-official`
- 봇 미초대: Discord Developer Portal → OAuth2 → URL 생성 → 서버 초대"

**하트비트 미등록 시:**
"- 크론 재등록이 필요합니다
  → `/loop 30m \"Run as background agent: Read HEARTBEAT.md and execute instructions. Respond HEARTBEAT_OK if nothing to report.\"`"

**기억 미유지 시:**
"- IDENTITY.md, USER.md가 비어있으면 부트스트랩을 다시 실행해주세요
- MEMORY.md에 기록이 없으면 하트비트가 아직 실행 전일 수 있어요"

### 4. 재검증 안내

실패 항목이 있으면:

"**해결 후 다시 검증하고 싶으면 워크플로우를 검증 모드(V)로 다시 실행해주세요!**"

### 5. 마무리

"**검증 완료! 📋**

문제가 있으면 언제든 검증 모드로 다시 확인할 수 있어요.
궁금한 거 있으면 봇한테 물어보세요! 😊"

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- 전체 검증 결과가 명확하게 요약됨
- 통과 항목은 축하, 실패 항목은 해결 방법 제시
- 사용자가 무엇을 해야 하는지 명확히 알고 있음
- 재검증 방법이 안내됨

### ❌ SYSTEM FAILURE:

- 결과 요약 없이 끝남
- 실패 항목의 해결 방법 미제시
- 재검증 방법 안내 누락

**Master Rule:** 실패 항목이 있으면 반드시 구체적인 해결 방법을 제시하세요. 사용자가 혼자 해결할 수 있어야 합니다.
