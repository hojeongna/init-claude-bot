# init-claude-bot

Claude Code를 **나만의 AI 개인비서**로 세팅하는 온보딩 워크플로우.

[오픈 클로(Open Claw)])의 개인비서 컨셉을 기반으로, 나주임이 실제 운용하면서 쌓은 노하우를 반영해 구조와 자동화를 변형한 버전입니다. 디스코드/텔레그램 연동, 크론 자동화, Peers 멀티세션 운영 등 실전에서 검증된 설정이 들어있습니다.

## 특징

- **대화형 온보딩** — 워크플로우를 따라가면 누구나 개인비서를 완성할 수 있습니다
- **봇 정체성 시스템** — 이름, 성격, 원칙을 대화로 정하고 파일로 유지합니다
- **하트비트 자동화** — 30분마다 디스코드 대화를 수집하고 파일을 자동 업데이트합니다
- **Peers 멀티세션** — 메인 세션과 크론 세션을 분리해 안정적으로 운영합니다
- **세션 중단/재개** — 언제든 끊었다 이어갈 수 있습니다
- **검증 모드** — 세팅이 제대로 됐는지 자동으로 확인합니다

## 빠른 시작

### 필요 환경

- Claude Code v2.1.80+
- Bun 런타임
- Python 3
- Discord 또는 Telegram 계정

### 설치 및 실행

```bash
git clone https://github.com/hojeongna/init-claude-bot.git
cd init-claude-bot
```

Claude Code를 실행한 후:

```
/init-claude-bot
```

6단계를 따라가면 완성됩니다.

## 워크플로우 흐름

```
/init-claude-bot
│
├── [C] 새로 만들기
│   │
│   ├── Step 1: 환경 체크 (Claude Code, Bun, Python)
│   ├── Step 2: 핵심 파일 세팅 (12개 템플릿 배치)
│   ├── Step 3: 메시징 플랫폼
│   │   ├── [D] Discord — 봇 생성 → 세션 재시작 → 연결 검증
│   │   ├── [T] Telegram — 봇 생성 → 세션 재시작 → 연결 검증
│   │   └── [S] 스킵
│   ├── Step 4: 자동화 크론
│   │   ├── [P] Peers 모드 — MCP 설치 → 크론 세션 분리 → 크론 등록
│   │   └── [S] Solo 모드 — 단일 세션에서 크론 등록
│   ├── Step 5: 부트스트랩 대화 (봇 정체성 확립)
│   └── Step 6: 완료!
│
└── [V] 검증하기 — 기존 세팅 점검 (3단계)
```

세션이 중단되면 `/init-claude-bot`을 다시 실행하면 자동으로 이어갑니다.

## 프로젝트 구조

```
init-claude-bot/
├── .claude/
│   └── commands/
│       └── init-claude-bot.md       ← /init-claude-bot 슬래시 커맨드
└── docs/
    ├── workflow.md                  ← 메인 워크플로우 (모드 선택 & 라우팅)
    ├── steps-c/                     ← 생성 모드 스텝 (15개)
    │   ├── step-01-init.md          환경 체크 & 상태 파일 생성
    │   ├── step-01b-continue.md     중단 후 재개 라우팅
    │   ├── step-02-setup-files.md   템플릿 복사 & 디렉토리 생성
    │   ├── step-03-messaging-choice.md    메시징 플랫폼 선택
    │   ├── step-03a-discord-setup.md      Discord 봇 생성 & 플러그인 설치
    │   ├── step-03b-discord-connect.md    Discord 연결 검증 & 스크립트 세팅
    │   ├── step-03c-telegram-setup.md     Telegram 봇 생성 & 플러그인 설치
    │   ├── step-03d-telegram-connect.md   Telegram 연결 검증
    │   ├── step-04-automation-choice.md   자동화 모드 선택 (Peers/Solo)
    │   ├── step-04a-peers-install.md      claude-peers-mcp 설치
    │   ├── step-04b-peers-verify.md       Peers 연결 검증
    │   ├── step-04c-peers-cron.md         Peers 모드 크론 등록
    │   ├── step-04s-solo-cron.md          Solo 모드 크론 등록
    │   ├── step-05-bootstrap.md           봇 정체성 대화
    │   └── step-06-complete.md            온보딩 완료
    ├── steps-v/                     ← 검증 모드 스텝 (3개)
    │   ├── step-v-01-init.md        검증 시작
    │   ├── step-v-02-check.md       항목별 검증
    │   └── step-v-03-report.md      검증 리포트
    ├── data/                        ← 참조 데이터
    │   ├── discord-scripts.md       Python 스크립트 코드
    │   ├── env-var-setup.md         OS별 환경변수 설정 가이드
    │   └── setup-checklist.md       사용 가이드
    └── templates/                   ← 프로젝트 루트에 배치될 템플릿 (11개)
        ├── CLAUDE.md                프로젝트 규칙 (세션 시작, 크론, 메모리 등)
        ├── BOOTSTRAP.md             첫 대화 스크립트 (사용 후 삭제됨)
        ├── SOUL.md                  봇 성격 & 원칙
        ├── IDENTITY.md              봇 정체성 (이름, 이모지, 바이브)
        ├── USER.md                  유저 프로필
        ├── CONTEXT.md               작업 환경 정보
        ├── TRIGGERS.md              트리거 → 행동 매핑
        ├── HEARTBEAT.md             30분 주기 작업 정의
        ├── MEMORY.md                장기 기억
        ├── DISCORD.md               Discord API 확장 스크립트
        └── CRON-REGISTRY.md         크론 레지스트리
```

## 세팅 후 생성되는 파일

온보딩이 완료되면 프로젝트 루트에 다음 파일들이 배치됩니다:

```
프로젝트 루트/
├── .claude/settings.json     SessionStart 훅 (세션마다 파일 자동 로드)
├── CLAUDE.md                 프로젝트 규칙
├── SOUL.md                   봇 성격
├── IDENTITY.md               봇 정체성
├── USER.md                   유저 정보
├── CONTEXT.md                작업 환경
├── TRIGGERS.md               자동화 트리거
├── HEARTBEAT.md              하트비트 지시서
├── MEMORY.md                 장기 기억
├── DISCORD.md                Discord API 확장
├── memory/
│   ├── YYYY-MM-DD.md         데일리 로그
│   ├── CHANGELOG.md          파일 변경 이력
│   └── cron-registry.md      크론 백업
└── scripts/
    ├── fetch_discord.py      디스코드 채팅 수집
    └── change_avatar.py      봇 프사 변경 (선택)
```

## 핵심 시스템

### 하트비트 (30분 주기)

봇의 자율 학습 루프입니다.

1. `fetch_discord.py`로 최근 30분간 디스코드 대화를 수집
2. 대화를 분석해서 관련 파일을 업데이트 (USER.md, CONTEXT.md, SOUL.md 등)
3. 주요 내용을 `memory/YYYY-MM-DD.md` 데일리 로그에 기록
4. 변경 이력을 `memory/CHANGELOG.md`에 기록

유저에게 알림 없이 조용히 동작합니다 (Silent Automation).

### 크론 자동화

| 크론 | 주기 | 역할 |
|------|------|------|
| 하트비트 | 30분 | 대화 수집 & 파일 업데이트 |
| 최신화 루프 | 5일 | 전체 설정 파일 리뷰 |
| 크론 스냅샷 | 1일 | 크론 목록을 레지스트리에 백업 |
| 크론 재등록 | 5일 | 7일 만료 방지용 전체 재등록 |

### 파일 소유권

정보는 **한 곳에만** 기록합니다. 중복 기록 금지.

- `SOUL.md` — 봇이 **어떻게** 행동하는가
- `IDENTITY.md` — 봇이 **누구**인가
- `USER.md` — 유저가 **누구**인가
- `CONTEXT.md` — **작업에 필요한 정보**
- `TRIGGERS.md` — **어떤 요청에 어떻게 반응**하는가
- `MEMORY.md` — **무슨 일이 있었는가**

## Peers 멀티세션 시스템

### 왜 필요한가

Claude Code의 크론은 세션에 종속되어 있어서, 세션을 닫으면 크론도 사라집니다. 또한 7일 후 자동 만료됩니다. Peers 시스템은 **크론 전용 세션**을 분리해서 이 문제를 해결합니다.

### 구조

```
┌─────────────────────┐     claude-peers-mcp     ┌─────────────────────┐
│                     │◄──────────────────────►│                     │
│    메인 세션         │    list_peers            │    크론 세션         │
│                     │    send_message           │                     │
│  - 유저 대화         │    check_messages         │  - 하트비트 (30분)   │
│  - 헬스체크 (1시간)  │                           │  - 최신화 (5일)     │
│                     │                           │  - 스냅샷 (1일)     │
│                     │                           │  - 재등록 (5일)     │
└─────────────────────┘                           └─────────────────────┘
     터미널 1                                           터미널 2
```

### 작동 원리

[claude-peers-mcp](https://github.com/louislva/claude-peers-mcp)는 같은 머신의 Claude Code 세션끼리 서로 발견하고 메시지를 주고받을 수 있게 하는 MCP 서버입니다.

**핵심 도구:**
- `list_peers(scope: "machine")` — 현재 실행 중인 Claude Code 세션 목록
- `set_summary(text)` — 세션에 라벨 부착 (예: "cron-worker")
- `send_message(peer_id, message)` — 다른 세션에 메시지 전송
- `check_messages()` — 받은 메시지 확인

**메인 세션**은 유저와 대화하면서 1시간마다 헬스체크로 크론 세션이 살아있는지 확인합니다. **크론 세션**은 모든 자동화를 담당하며, 메인 세션이 재시작되어도 크론이 유지됩니다.

### Peers vs Solo 비교

| | Peers | Solo |
|---|---|---|
| 터미널 | 2개 (메인 + 크론) | 1개 |
| 추가 설치 | claude-peers-mcp | 없음 |
| 크론 안정성 | 메인 세션 재시작해도 크론 유지 | 세션 닫으면 크론 소멸 |
| 크론 복원 | 크론 세션이 알아서 유지 | 세션 시작 시 레지스트리에서 복원 |
| 복잡도 | 보통 (두 번째 터미널 필요) | 단순 |

## 워크플로우 아키텍처

이 프로젝트는 BMAD의 **스텝 파일 아키텍처**를 따릅니다.

- **마이크로 파일 설계** — 각 스텝은 독립적인 지시 파일
- **JIT 로딩** — 현재 스텝만 메모리에 로드, 다음 스텝은 지시 전까지 로드하지 않음
- **순차 실행** — 건너뛰기, 순서 변경 금지
- **상태 추적** — `.claude-bot-status.json`으로 진행 상태 관리
- **중단/재개** — 어디서 끊어도 `step-01b`가 마지막 완료 지점으로 자동 라우팅

## 오픈 클로와의 차이점

오픈 클로의 개인비서 컨셉을 기반으로 하되, 실전 운용 경험을 반영해 다음을 변형했습니다:

- **대화형 워크플로우** — 설정 파일을 직접 수정하는 대신, 스텝별 가이드를 따라가면 완성
- **Peers 멀티세션** — 크론 전용 세션 분리로 안정적인 자동화 운영
- **자동 크론 복원** — 7일 만료 대응, 세션 재시작 시 레지스트리 기반 복원
- **부트스트랩 대화** — 봇 정체성을 양식이 아닌 자연스러운 대화로 확립
- **파일 소유권 규칙** — 정보 중복 방지를 위한 명확한 파일별 역할 분담
- **검증 모드** — 세팅 후 자동 점검 기능 내장

## 라이선스

MIT
