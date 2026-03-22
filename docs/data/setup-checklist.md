# init-claude-bot 사용 가이드

## 설치 방법

이 레포를 클론하거나 폴더를 통째로 복사하세요:

```bash
git clone <레포주소> init-claude-bot
cd init-claude-bot
```

그게 전부예요! `.claude/commands/`와 `docs/`가 모두 포함되어 있어서 바로 사용 가능합니다.

## 실행 방법

`init-claude-bot/` 폴더에서 Claude Code를 실행한 후:

```
/init-claude-bot
```

## 필요 환경

- Claude Code v2.1.80+
- claude.ai 로그인 (API 키 아님)
- Bun 런타임
- Python 3
- Discord 또는 Telegram 계정

모두 워크플로우 진행 중에 안내됩니다.

## 워크플로우 흐름

1. 환경 체크
2. 핵심 파일 세팅 (11개 템플릿 배치)
3. 메시징 플랫폼 선택 (디스코드 / 텔레그램 / 스킵) & 연동 (세션 재시작 필요)
4. 자동화 크론 등록
5. 봇 정체성 대화 (부트스트랩)
6. 완료!

## 세션 중단/재개

메시징 플랫폼 설정 중 세션을 재시작해야 합니다.
재시작 후 `/init-claude-bot` 을 다시 실행하면 자동으로 중단한 곳에서 이어갑니다.

## 검증

세팅 완료 후 검증하려면 `/init-claude-bot` 실행 후 `V` 를 선택하세요.

## 폴더 구조

```
init-claude-bot/
├── .claude/
│   └── commands/
│       └── init-claude-bot.md    ← /init-claude-bot 커맨드
└── docs/
    ├── workflow.md               ← 메인 워크플로우
    ├── data/                     ← 참조 데이터
    ├── steps-c/                  ← 생성 스텝 (15개)
    ├── steps-v/                  ← 검증 스텝 (3개)
    └── templates/                ← 한국어 템플릿 (11개)
```
