# Cron Registry

_크론이 추가/수정/삭제될 때마다 이 파일을 업데이트할 것._

## 크론 모드

**모드:** daemon

---

## 응답 모드 설명

각 크론의 **응답** 컬럼은 실행 후 유저에게 어떻게 알릴지를 결정합니다:

| 모드 | 설명 |
|------|------|
| `silent` | 조용히 처리. 유저에게 아무 알림 없음. 결과 코드만 내부 반환 |
| `notify` | 실행 결과를 유저에게 알림. 문제 발생 시 즉시 알림 |
| `conditional` | 평소엔 silent, 이슈가 있을 때만 유저에게 알림 |

---

## 등록된 크론

| 이름 | 스케줄 | 방식 | 응답 | 설명 |
|------|--------|------|------|------|
| 하트비트 | `4,34 * * * *` | claude -p | silent | 대화 분석 · 파일 업데이트 |

## 스케줄러 관리

- 데몬: `scripts/scheduler_daemon.py`
- LaunchAgent: `~/Library/LaunchAgents/com.[봇이름].scheduler.plist`
- 크론 추가/수정: `/scheduler-manage` 스킬 사용
- 상태 조회: `dashboard/cron-status.json`
- 로그: `memory/cron.log`, `dashboard/cron-log.json`

---

## 변경 이력

_(크론 추가/수정/삭제 시 자동 기록)_
