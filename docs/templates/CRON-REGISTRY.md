# Cron Registry

_크론이 추가/수정/삭제될 때마다 이 파일을 업데이트할 것._
_세션 시작(startup) 시 이 파일을 읽고 모든 크론을 재등록할 것._

## 크론 모드

_온보딩 시 선택한 모드가 기록됩니다._

**모드:** _(peers 또는 solo — step-04에서 자동 기록)_

---

## Peers 모드 (크론 세션 분리)

_peers 모드 선택 시 아래 형식으로 기록됩니다._

### 크론 세션 크론

| 이름 | 스케줄 | 프롬프트 |
|------|--------|---------|
| 하트비트 | `*/30 * * * *` | Run as background agent: Read HEARTBEAT.md and execute instructions. Respond HEARTBEAT_OK if nothing to report. |
| 최신화 루프 | `3 9 */5 * *` | Run as background agent: Review all config files for outdated info. Update as needed. Log changes to memory/CHANGELOG.md. Respond REFRESH_OK. |
| 크론 스냅샷 | `7 0 * * *` | Run as background agent: Run CronList. Update memory/cron-registry.md. Respond SNAPSHOT_OK. |
| 크론 재등록 | `11 9 */5 * *` | Run as background agent: Read memory/cron-registry.md. Delete ALL existing crons. Re-register all. Respond REREGISTER_OK. |

### 메인 세션 크론

| 이름 | 스케줄 | 프롬프트 |
|------|--------|---------|
| 헬스체크 | `0 * * * *` | Run as background agent: Call list_peers (scope: machine). Check if cron-worker peer exists. If found, HEALTH_OK. If not, notify user. |

---

## Solo 모드 (단일 세션)

_solo 모드 선택 시 아래 형식으로 기록됩니다._

### 현재 등록된 크론

| 이름 | 스케줄 | 프롬프트 |
|------|--------|---------|
| 하트비트 | `*/30 * * * *` | Run as background agent: Read HEARTBEAT.md and execute instructions. Respond HEARTBEAT_OK if nothing to report. |
| 최신화 루프 | `3 9 */5 * *` | Run as background agent: Review all config files for outdated info. Update as needed. Log changes to memory/CHANGELOG.md. Respond REFRESH_OK. |
| 크론 스냅샷 | `7 0 * * *` | Run as background agent: Run CronList. Update memory/cron-registry.md. Respond SNAPSHOT_OK. |
| 크론 재등록 | `11 9 */5 * *` | Run as background agent: Read memory/cron-registry.md. Delete ALL existing crons. Re-register all. Respond REREGISTER_OK. |

---

## 변경 이력

_(크론 추가/수정/삭제 시 자동 기록)_

## 스냅샷 이력

_(1일마다 크론 스냅샷이 자동 기록)_
