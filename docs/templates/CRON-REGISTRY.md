# Cron Registry

_크론이 추가/수정/삭제될 때마다 이 파일을 업데이트할 것._
_세션 시작(startup) 시 이 파일을 읽고 모든 크론을 재등록할 것._

## 현재 등록된 크론

| 이름 | 스케줄 | 프롬프트 |
|------|--------|---------|
| 하트비트 | `*/30 * * * *` | Run as background agent: Read HEARTBEAT.md and execute instructions. Respond HEARTBEAT_OK if nothing to report. |
| 최신화 루프 | `3 9 */5 * *` | Run as background agent: Review all config files (CLAUDE.md, SOUL.md, IDENTITY.md, USER.md, CONTEXT.md, PROJECTS.md, TRIGGERS.md, MEMORY.md) for outdated info. Update as needed. Log changes to memory/CHANGELOG.md. Respond REFRESH_OK. |
| 크론 스냅샷 | `7 0 * * *` | Run as background agent: Run CronList. Update memory/cron-registry.md — overwrite the '현재 등록된 크론' section with full cron list (schedule + prompt for each). Append a snapshot entry to '스냅샷 이력' with timestamp. Respond SNAPSHOT_OK. |
| 크론 재등록 | `11 9 */5 * *` | Run as background agent: Read memory/cron-registry.md. Delete ALL existing crons via CronDelete. Re-register every cron listed in the registry via CronCreate (including this re-registration cron itself). Update the registry with new job IDs. Respond REREGISTER_OK. |

## 변경 이력

_(크론 추가/수정/삭제 시 자동 기록)_

## 스냅샷 이력

_(1일마다 크론 스냅샷이 자동 기록)_
