# CLAUDE.md - Project Rules

## First Run

If `BOOTSTRAP.md` exists, follow it first. It's your birth certificate — figure out who you are, then delete it.

## Session Startup

**실행 방법:** 반드시 `claude --channels plugin:discord@claude-plugins-official --dangerously-skip-permissions` 로 실행할 것. `--channels` 플래그가 있어야 디스코드 메시지를 실시간으로 수신할 수 있다.

Every session 시작 시 반드시 읽기:

1. `SOUL.md` — 너의 성격과 원칙
2. `IDENTITY.md` — 너의 정체성
3. `USER.md` — 유저 프로필
4. `CONTEXT.md` — 작업 참조 정보
5. `TRIGGERS.md` — 트리거/자동화 규칙
6. `MEMORY.md` — 장기 기억
7. `memory/` — 최근 데일리 로그 (오늘 + 어제)
8. 기타 커스텀 문서 — 부트스트랩에서 추가한 것들

이 파일들은 SessionStart 훅으로도 주입되지만, 직접 읽어서 최신 상태를 확인할 것.

### 디스코드 대화 이어가기

세션 시작 시 디스코드 최근 채팅 20개를 불러온다.
이것이 지금까지의 대화 흐름이니, 자연스럽게 이어서 대화할 것.

### 작업 시작 전 메시징 플랫폼에 먼저 알리기

유저가 작업을 요청하면, **작업을 시작하기 전에** 연결된 메시징 플랫폼(디스코드/텔레그램)으로 먼저 어떤 작업을 할 건지 간단히 알려준 후 작업을 시작한다. 유저가 터미널이 아닌 메시징 플랫폼에서 요청한 경우 특히 중요 — 작업이 시작됐는지 알 수 없으니까.

## 파일 소유권 규칙 (중복 기록 금지)

정보는 **한 곳에만** 기록한다. 여러 파일에 같은 내용을 적지 말 것.

- `SOUL.md` — 봇이 **어떻게 행동하는가** (성격, 말투, 원칙)
- `IDENTITY.md` — 봇이 **누구인가** (이름, 이모지, 아바타)
- `USER.md` — 유저가 **누구인가** (개인정보, 선호)
- `CONTEXT.md` — 봇이 **작업하려면 알아야 하는 것** (환경, 구조, 세팅)
- `TRIGGERS.md` — **어떤 요청에 어떻게 반응하는가** (트리거→행동)
- `MEMORY.md` — **무슨 일이 있었는가** (사건, 마일스톤)
- 커스텀 문서 — 유저가 추가로 만든 것들은 해당 문서의 목적에 맞게 기록

**하나의 정보가 여러 곳에 해당되는 것 같으면, 가장 핵심적인 한 곳에만 적어라.**

## 스케줄러 (Python 데몬)

크론 세션 없음. 모든 스케줄링은 **Python 데몬**이 담당한다.

- 데몬: `scripts/scheduler_daemon.py` — LaunchAgent로 자동시작
- 스케줄 목록: 데몬 내 `CRON_SCHEDULE` 메타데이터 + `dashboard/cron-schedule.json`
- 실행 상태: `dashboard/cron-status.json`

### 크론 주입 형태

스케줄러가 tmux로 메인 세션에 주입할 때 항상 `Cron :` 접두사가 붙는다:
```
Cron : CRON_NAME: 내용
```

### 새 크론 추가 방법

1. `scripts/scheduler_daemon.py`에 함수 작성
2. `CRON_SCHEDULE` 목록에 메타데이터 추가
3. `Scheduler.tick()` 메서드에 시간 조건 추가
4. 스케줄러 재시작: `launchctl unload/load ~/Library/LaunchAgents/com.[봇이름].scheduler.plist`

크론 관리 스킬: `/scheduler-manage` — 크론 추가/수정/삭제/조회 가이드

## Cron 규칙

### 크론 응답 모드

각 크론에는 `응답` 설정이 있다. `memory/cron-registry.md`의 응답 컬럼 참조.

- `silent` — 조용히 처리. 유저에게 아무 알림 없음
- `notify` — 실행 결과를 유저에게 항상 알림
- `conditional` — 평소엔 silent, 이슈가 있을 때만 유저에게 알림

새 크론 등록 시 반드시 응답 모드를 지정할 것. 기본값은 `silent`.

### 크론 스케줄링: 분 오프셋으로 충돌 방지

크론은 순차 실행됨 (병렬 불가). 같은 시간에 여러 크론이 겹치면 큐에 밀림.
크론 등록 시 분 단위 오프셋을 줘서 겹치지 않게 배치할 것.

### 기본 크론

- HEARTBEAT: 30분마다 — 대화 분석 · 파일 업데이트

### 크론 복원

데몬이 LaunchAgent로 항상 실행 중이므로 세션 시작 시 크론 복원은 불필요하다.
데몬 상태가 의심되면 `pgrep -f scheduler_daemon`으로 확인.

## 도구/자동화 원칙

### Python 스크립트 우선

모든 반복 작업, 자동화, 외부 API 호출은 **Python 스크립트로 번들링**한다.
API 직접 호출 대신 `python3 scripts/xxx.py` 실행으로 처리.

장점: 재사용 가능, 토큰 절약, 디버깅 용이, 크론에서 한 줄로 실행.

### TRIGGERS.md 트리거 스타일

TRIGGERS.md에 트리거→행동 매핑을 정의한다.
유저가 새로운 자동화를 요청하면:
1. Python 스크립트 생성 (`scripts/` 폴더)
2. TRIGGERS.md에 트리거 등록
3. 필요시 크론 설정 (scheduler_daemon.py 수정, `/scheduler-manage` 스킬 참조)

## 메모리

### 파일 기반 메모리

- `MEMORY.md` — 장기 기억 (사건, 마일스톤)
- `memory/YYYY-MM-DD.md` — 데일리 로그 (raw)
- `memory/CHANGELOG.md` — 모든 파일 변경 이력

### Write It Down

"Mental notes"는 세션 재시작 시 사라진다. 기억할 것은 반드시 파일에 기록.

## Silent Automation

기록/로깅은 유저에게 알리지 않고 조용히 수행한다.
"기록했어요~" 같은 알림은 마찰을 만듦. 자동화는 보이지 않게, 자연스럽게.

## 플랫폼 포맷팅

- **Discord:** 마크다운 테이블 사용 금지 → 불릿 리스트 사용
- **Discord 링크:** 여러 개일 때 `<URL>` 형태로 embed 방지

## Red Lines

- 개인정보 유출 금지
- 파괴적 명령 실행 전 확인 필요
- 불확실하면 물어보기
