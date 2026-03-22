---
name: 'step-04a-peers-install'
description: 'claude-peers-mcp 설치 및 MCP 등록'

nextStepFile: './step-04b-peers-verify.md'
statusFile: '.claude-bot-status.json'
---

# Step 4a: Peers 설치

## STEP GOAL:

claude-peers-mcp를 설치하고 MCP 서버로 등록합니다.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER generate content without user input
- 📖 CRITICAL: Read the complete step file before taking any action
- 🔄 CRITICAL: When loading next step with 'C', ensure entire file is read
- 📋 YOU ARE A FACILITATOR, not a content generator
- ✅ 항상 한국어로 소통하세요

### Role Reinforcement:

- ✅ 당신은 친근하고 격려하는 온보딩 가이드입니다
- ✅ 각 단계가 왜 필요한지 설명합니다

### Step-Specific Rules:

- 🎯 peers 설치에만 집중하세요
- 🚫 크론 등록은 하지 마세요 (다음 스텝에서)
- 💬 설치 과정을 단계별로 안내하세요
- 🔧 플랫폼별 차이를 정확히 처리하세요

## EXECUTION PROTOCOLS:

- 🎯 설치 명령 실행 및 결과 확인
- 💾 상태 파일 업데이트
- 📖 세션 재시작 안내

## CONTEXT BOUNDARIES:

- step-04-automation-choice에서 Peers 모드를 선택한 상태입니다
- Bun은 step-01에서 이미 설치 확인 완료
- 설치 후 세션 재시작이 필요합니다 (MCP 서버 로드)

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly.

### 1. Bun 확인

Bun이 설치되어 있는지 `bun --version`으로 확인합니다.

**IF 설치됨:** "✅ Bun이 이미 설치되어 있어요! 바로 진행할게요."

**IF 미설치:** "❌ Bun이 필요해요! step-01에서 설치했어야 하는데... 다시 확인해볼게요."
Bun 설치를 안내하고 재확인합니다.

### 2. 설치 설명

"**claude-peers-mcp를 설치할 거예요! 🔗**

이건 여러 Claude Code 세션끼리 서로 대화할 수 있게 해주는 도구예요.
크론 전용 세션을 따로 띄워서 운영하려면 이게 필요해요!"

### 3. 저장소 클론 및 설치

다음 명령을 순서대로 실행합니다:

```bash
git clone https://github.com/louislva/claude-peers-mcp.git ~/claude-peers-mcp
cd ~/claude-peers-mcp
bun install
```

각 명령의 결과를 확인하고 에러가 있으면 사용자에게 알려주세요.

설치 성공 시: "✅ claude-peers-mcp 설치 완료!"

### 4. 플랫폼 감지 및 패치

플랫폼을 확인합니다.

**IF Windows:**

"**Windows 환경이네요! 경로 패치를 적용할게요.**

Windows에서는 파일 경로 형식이 달라서 수정이 필요해요."

`~/claude-peers-mcp/server.ts`에서 다음을 수정합니다:

**수정 전:**
```typescript
const BROKER_SCRIPT = new URL("./broker.ts", import.meta.url).pathname;
```

**수정 후:**
```typescript
const BROKER_SCRIPT = (() => {
  const p = new URL("./broker.ts", import.meta.url).pathname;
  // Windows: strip leading slash from /C:/... paths
  return process.platform === "win32" && p.match(/^\/[A-Za-z]:/) ? p.slice(1) : p;
})();
```

패치 적용 후: "✅ Windows 경로 패치 완료!"

**IF Mac/Linux:**

"✅ Mac/Linux에서는 추가 패치 없이 바로 사용 가능해요!"

### 5. MCP 서버 등록

다음 명령으로 모든 Claude Code 세션에서 사용 가능하게 등록합니다:

```bash
claude mcp add --scope user --transport stdio claude-peers -- bun ~/claude-peers-mcp/server.ts
```

등록 성공 시: "✅ MCP 서버 등록 완료!"

### 6. 설치 결과 요약

"**설치 완료! 🎉**

- ✅ claude-peers-mcp 클론 및 설치
- ✅ 플랫폼 패치 (해당 시)
- ✅ MCP 서버 등록

**⚠️ 중요:** MCP 서버를 사용하려면 **세션을 재시작**해야 해요!
지금 세션을 종료하고 다시 시작해주세요.

다시 돌아오면 워크플로우가 자동으로 이어서 진행돼요! 😊"

### 7. 상태 업데이트

`{statusFile}`의 `stepsCompleted`에 `step-04a-peers-install`을 추가합니다.

**중요:** 여기서 세션이 종료됩니다. 다음 세션에서 step-01b를 통해 step-04b-peers-verify로 라우팅됩니다.

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- Bun 설치 확인됨
- claude-peers-mcp 클론 및 설치 완료
- 플랫폼 감지 후 필요한 패치 적용
- MCP 서버 등록 완료
- 상태 파일 업데이트됨
- 사용자에게 세션 재시작 안내됨

### ❌ SYSTEM FAILURE:

- Bun 없이 설치 시도
- 플랫폼 패치 누락 (Windows에서)
- MCP 등록 누락
- 세션 재시작 안내 없이 다음 스텝 진행
- 상태 파일 업데이트 누락

**Master Rule:** 설치 후 반드시 세션 재시작을 안내해야 합니다. MCP 서버는 세션 시작 시에만 로드됩니다.
