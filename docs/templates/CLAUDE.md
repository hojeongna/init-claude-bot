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

## 세션 역할 (Peers 모드 전용)

`memory/cron-registry.md`의 크론 모드가 `peers`인 경우에만 적용된다.

### 역할 판단 (세션 시작 시)

1. `list_peers` (scope: machine)로 피어 목록을 확인한다
2. summary에 `cron-worker`가 있는 피어가 있으면 → 나는 **메인 세션**
3. 없고, 내가 크론 전담으로 시작됐으면 → 나는 **크론 세션**

### 메인 세션 역할

- `set_summary`로 자신의 역할을 설정한다 (예: "메인 세션 — [작업 내용]")
- 크론은 **헬스체크 1개만** 등록 (크론 세션 생존 확인)
- 크론 복원/재등록 로직 수행하지 않음
- 유저 대화에 집중

### 크론 세션 역할

- 시작 시 `set_summary("cron-worker")`로 자신을 등록
- 모든 크론을 **백그라운드 서브에이전트**로 등록
- 메인 스레드는 항상 대기 상태 유지 (메시지 수신/응답)
- 유저에게 직접 말 걸지 않음
- 메인 세션에서 메시지가 오면 즉시 응답

## Cron 규칙

### 모든 크론은 백그라운드 서브에이전트로 실행

크론이 실행될 때 메인 대화를 방해하지 않도록 반드시 백그라운드 에이전트로 처리한다.

```
크론 프롬프트 예시:
"Run as background agent: Read HEARTBEAT.md and execute instructions. Respond HEARTBEAT_OK if nothing to report."
```

### 유저가 등록 요청하거나 직접 등록할 때도 서브에이전트로

유저가 "~해줘", "~등록해줘", "~모니터링해줘" 등 반복 작업을 요청하거나, `/loop` 등으로 직접 크론을 등록하려 할 때도 항상 프롬프트에 `"Run as background agent: ..."` 를 붙여서 백그라운드 서브에이전트로 실행되도록 설정한다. 유저가 명시적으로 포그라운드를 원하지 않는 한 예외 없음.

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

- HEARTBEAT: 30분마다 (`*/30 * * * *`)
- 최신화 루프: 5일마다 (`3 9 */5 * *`) — 전체 설정 파일 리뷰 및 업데이트
- 크론 스냅샷: 1일마다 (`7 0 * * *`) — 크론 목록을 레지스트리에 백업
- 크론 재등록: 5일마다 (`11 9 */5 * *`) — 7일 만료 방지용 전체 재등록

### 크론 레지스트리 (memory/cron-registry.md)

크론은 세션 종료 시 소멸하고, 7일 후 자동 만료된다.
이를 복원하기 위해 `memory/cron-registry.md`에 전체 크론 정보를 기록한다.

**필수 규칙:**
- 크론을 추가/수정/삭제할 때마다 즉시 `memory/cron-registry.md`를 업데이트할 것
- 변경 이력 섹션: [YYYY-MM-DD HH:MM] 추가/수정/삭제 내용 기록

### 크론 복원 (세션 시작 시)

**Solo 모드만 해당** — Peers 모드에서는 크론 세션이 살아있으므로 복원 불필요.

SessionStart(startup) 훅이 `memory/cron-registry.md`를 주입하고 `CRON_RECOVERY_CHECK` 지시를 보낸다.

**크론 복원 절차 (Solo 모드, 매 세션 시작 시):**

1. `memory/cron-registry.md`에서 크론 모드를 확인한다
2. **Peers 모드면:** `list_peers`로 cron-worker 피어 생존만 확인하고 끝
3. **Solo 모드면:**
   a. `CronList`로 현재 등록된 크론 목록을 확인한다
   b. `memory/cron-registry.md`의 등록 목록과 비교한다
   c. 누락된 크론이 있으면 `CronCreate`로 재등록한다
   d. 새로운 Job ID로 `memory/cron-registry.md`를 업데이트한다
   e. 이미 모든 크론이 등록되어 있으면 아무것도 하지 않는다

이 작업은 백그라운드 에이전트가 아닌 **메인 세션에서 즉시** 수행할 것.
크론 복원은 유저에게 알리지 않고 Silent Automation으로 처리한다.

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
3. 필요시 크론 설정

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
