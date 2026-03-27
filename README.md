# init-claude-bot

Claude Code를 **나만의 AI 개인비서**로 세팅하는 온보딩 워크플로우.

[오픈 클로(Open Claw)])의 개인비서 컨셉을 기반으로, 실제 운용하면서 쌓은 노하우를 반영해 구조와 자동화를 변형한 버전입니다. 디스코드/텔레그램 연동, 크론 자동화, Python 스케줄러 데몬 운영 등 실전에서 검증된 설정이 들어있습니다.

## 특징

- **대화형 온보딩** — 워크플로우를 따라가면 누구나 개인비서를 완성할 수 있습니다
- **봇 정체성 시스템** — 이름, 성격, 원칙을 대화로 정하고 파일로 유지합니다
- **하트비트 자동화** — 30분마다 디스코드 대화를 수집하고 파일을 자동 업데이트합니다
- **Python 스케줄러 데몬** — 세션 독립적인 크론 시스템으로 안정적으로 운영합니다
- **세션 중단/재개** — 언제든 끊었다 이어갈 수 있습니다
- **검증 모드** — 세팅이 제대로 됐는지 자동으로 확인합니다

## 빠른 시작

### 필요 환경

- Claude Code v2.1.80+
- Bun 런타임
- Python 3
- tmux (Daemon 모드 선택 시)
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
│   │   ├── [N] 새로 설치 — Python 데몬 + tmux + LaunchAgent
│   │   └── [U] Update — 기존 Peers 모드에서 Daemon으로 업그레이드
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
│       ├── init-claude-bot.md       ← /init-claude-bot 슬래시 커맨드
│       └── scheduler-manage.md      ← /scheduler-manage 스킬
└── docs/
    ├── workflow.md                  ← 메인 워크플로우 (모드 선택 & 라우팅)
    ├── steps-c/                     ← 생성 모드 스텝
    │   ├── step-01-init.md          환경 체크 & 상태 파일 생성
    │   ├── step-01b-continue.md     중단 후 재개 라우팅
    │   ├── step-02-setup-files.md   템플릿 복사 & 디렉토리 생성
    │   ├── step-03-messaging-choice.md    메시징 플랫폼 선택
    │   ├── step-03a-discord-setup.md      Discord 봇 생성 & 플러그인 설치
    │   ├── step-03b-discord-connect.md    Discord 연결 검증 & 스크립트 세팅
    │   ├── step-03c-telegram-setup.md     Telegram 봇 생성 & 플러그인 설치
    │   ├── step-03d-telegram-connect.md   Telegram 연결 검증
    │   ├── step-04-automation-choice.md   자동화 설정 (새로 설치 / Peers 업그레이드)
    │   ├── step-04a-daemon-setup.md       Python 스케줄러 데몬 설치
    │   ├── step-04b-daemon-cron.md        크론 등록 (하트비트 필수 + 선택)
    │   ├── step-04u-migrate.md            Peers → Daemon 마이그레이션
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
    └── templates/                   ← 프로젝트 루트에 배치될 템플릿 (10개)
        ├── CLAUDE.md                프로젝트 규칙 (세션 시작, 크론, 메모리 등)
        ├── BOOTSTRAP.md             첫 대화 스크립트 (사용 후 삭제됨)
        ├── SOUL.md                  봇 성격 & 원칙
        ├── IDENTITY.md              봇 정체성 (이름, 이모지, 바이브)
        ├── USER.md                  유저 프로필
        ├── CONTEXT.md               작업 환경 정보
        ├── TRIGGERS.md              트리거 → 행동 매핑
        ├── HEARTBEAT.md             30분 주기 작업 정의
        ├── MEMORY.md                장기 기억
        └── DISCORD.md               Discord API 확장 스크립트
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
│   └── CHANGELOG.md          파일 변경 이력
├── scripts/
│   ├── fetch_discord.py      디스코드 채팅 수집
│   ├── scheduler_daemon.py   스케줄러 데몬 (Daemon 모드)
│   └── change_avatar.py      봇 프사 변경 (선택)
└── .claude/commands/
    └── scheduler-manage.md   스케줄러 관리 스킬
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

| 크론 | 주기 | 응답 | 역할 | 필수 |
|------|------|------|------|------|
| 하트비트 | 30분 | silent | 대화 수집 & 파일 업데이트 | ✅ |
| 최신화 루프 | 5일 | silent | 전체 설정 파일 리뷰 | 선택 |

**응답 모드:** `silent` (조용히 처리) / `notify` (항상 알림) / `conditional` (이슈 시만 알림)

하트비트만 필수이고 나머지 크론은 유저가 필요에 따라 추가합니다. `/scheduler-manage` 스킬로 관리할 수 있습니다.

### 파일 소유권

정보는 **한 곳에만** 기록합니다. 중복 기록 금지.

- `SOUL.md` — 봇이 **어떻게** 행동하는가
- `IDENTITY.md` — 봇이 **누구**인가
- `USER.md` — 유저가 **누구**인가
- `CONTEXT.md` — **작업에 필요한 정보**
- `TRIGGERS.md` — **어떤 요청에 어떻게 반응**하는가
- `MEMORY.md` — **무슨 일이 있었는가**

## Python 스케줄러 데몬

### 왜 필요한가

Claude Code의 내장 크론(CronCreate)은 세션에 종속되어 있어서, 세션을 닫으면 크론도 사라지고 7일 후 자동 만료됩니다. Python 스케줄러 데몬은 **세션과 독립적으로** 동작해서 이 문제를 해결합니다.

### 구조

```
┌─────────────────────┐     tmux send-keys      ┌─────────────────────┐
│                     │◄─────────────────────── │                     │
│    Claude 메인 세션  │   "Cron : {메시지}"      │   Python 데몬        │
│    (tmux 안에서)     │                         │   (LaunchAgent)      │
│                     │                         │                     │
│  - 유저 대화         │                         │  - 하트비트 (30분)   │
│  - 크론 결과 처리    │                         │  - 최신화 (5일)     │
│  - 디스코드 전달     │                         │  - 커스텀 크론들     │
│                     │                         │                     │
└─────────────────────┘                         └─────────────────────┘
     tmux 세션                                    백그라운드 프로세스
```

### 작동 원리

`scheduler_daemon.py`는 LaunchAgent로 항상 백그라운드에서 실행됩니다. 30초마다 시간을 체크하고, 해당 시간의 크론 함수를 실행합니다. 결과를 메인 Claude 세션에 전달해야 할 때는 `tmux send-keys`로 주입합니다.

**실행 방식 2가지:**
- **PY** — Python 스크립트 실행 (빠름, 토큰 절약)
- **AI** — `claude -p` 새 세션 실행 (판단/분석 필요할 때)

**장점:**
- 세션이 꺼져도 크론이 유지됨
- 7일 만료 없음
- LaunchAgent로 시스템 부팅 시 자동 시작
- tmux 세션명만 알면 됨 (피어 ID 변경 걱정 없음)

### 기존 Peers 모드에서 업그레이드

이전에 `claude-peers-mcp` + 크론 세션 분리 방식을 사용했다면, Step 4에서 **[U] Update**를 선택해서 Daemon 모드로 마이그레이션할 수 있습니다. 기존 크론을 자동으로 데몬에 이전하고, 피어 관련 설정을 정리합니다.

## 워크플로우 아키텍처

이 프로젝트는 BMAD의 **스텝 파일 아키텍처**를 따릅니다.

- **마이크로 파일 설계** — 각 스텝은 독립적인 지시 파일
- **JIT 로딩** — 현재 스텝만 메모리에 로드, 다음 스텝은 지시 전까지 로드하지 않음
- **순차 실행** — 건너뛰기, 순서 변경 금지
- **상태 추적** — `.claude-bot-status.json`으로 진행 상태 관리
- **중단/재개** — 어디서 끊어도 `step-01b`가 마지막 완료 지점으로 자동 라우팅

## 오픈 클로와의 차이점

오픈 클로의 개인비서 컨셉을 기반으로 하되, 실전 운용 경험을 반영해 다음을 변형했습니다:

### TOOLS.md → TRIGGERS.md (트리거 기반 자동화)

오픈 클로는 `TOOLS.md`에 도구 목록을 정의하지만, init-claude-bot은 **TRIGGERS.md**로 변경했습니다. 단순 도구 나열이 아니라 "유저가 이렇게 말하면 → 이렇게 반응한다"는 **트리거→행동 매핑** 방식이라, 봇이 유저 의도를 더 정확하게 파악하고 자동으로 반응합니다. 새 자동화 추가도 Python 스크립트 생성 + 트리거 등록으로 일관됩니다.

### 하트비트에 메모리 기능 탑재

오픈 클로의 하트비트는 단순 상태 체크 수준이지만, init-claude-bot의 하트비트는 **자율 학습 루프**입니다. 30분마다 메시징 플랫폼 대화를 수집하고, 내용을 분석해서 USER.md, CONTEXT.md, SOUL.md 등 관련 파일을 자동 업데이트합니다. 데일리 로그(`memory/YYYY-MM-DD.md`)와 변경 이력(`memory/CHANGELOG.md`)도 자동 기록해서, 세션이 재시작되어도 기억이 유지됩니다.

### 파이프라인 → Python 스케줄러 데몬

오픈 클로는 별도의 파이프라인/인프라로 자동화를 운영하지만, init-claude-bot은 **Python 스케줄러 데몬 하나**로 동일한 기능을 구현합니다. LaunchAgent로 자동시작되고, tmux를 통해 메인 Claude 세션에 결과를 주입합니다. 추가 인프라 없이 Python + tmux만으로 완성되며, 세션 독립적이라 메인 세션을 재시작해도 자동화가 끊기지 않습니다.

### 크론 응답 모드

각 크론에 `silent` / `notify` / `conditional` 응답 모드를 설정할 수 있습니다. 하트비트처럼 조용히 처리해야 하는 작업은 `silent`, 문제가 있을 때만 알려야 하는 작업은 `conditional`, 유저에게 항상 결과를 보고해야 하는 작업은 `notify`로 설정합니다.

### 기타 차이점

- **대화형 워크플로우** — 설정 파일을 직접 수정하는 대신, 스텝별 가이드를 따라가면 완성
- **`/scheduler-manage` 스킬** — 크론 추가/수정/삭제를 스킬 가이드로 체계적으로 관리
- **부트스트랩 대화** — 봇 정체성을 양식이 아닌 자연스러운 대화로 확립
- **파일 소유권 규칙** — 정보 중복 방지를 위한 명확한 파일별 역할 분담
- **검증 모드** — 세팅 후 자동 점검 기능 내장
- **Peers 마이그레이션** — 기존 Peers 사용자를 위한 자동 업그레이드 경로

## 라이선스

MIT
