---
name: 'step-04a-daemon-setup'
description: 'Python 스케줄러 데몬 설치 — tmux, scheduler_daemon.py, LaunchAgent 세팅'

nextStepFile: './step-04b-daemon-cron.md'
statusFile: '.claude-bot-status.json'
---

# Step 4a: Daemon 데몬 설치

## STEP GOAL:

Python 스케줄러 데몬을 설치하고, tmux + LaunchAgent로 세션과 독립적으로 동작하도록 설정합니다.

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

- 🎯 데몬 설치에만 집중하세요 (하트비트 포함)
- 🚫 추가 크론 등록은 하지 마세요 (다음 스텝에서)
- 💬 설치 과정을 단계별로 안내하세요
- 🔧 tmux 안에서 돌고 있는지 반드시 확인하세요

## EXECUTION PROTOCOLS:

- 🎯 tmux 확인, 스크립트 생성, LaunchAgent 등록
- 💾 상태 파일 업데이트
- 📖 데몬 정상 작동 확인

## CONTEXT BOUNDARIES:

- step-04-automation-choice에서 Daemon 모드를 선택한 상태입니다
- 하트비트는 필수 — 이 스텝에서 함께 등록합니다
- 추가 크론은 step-04b에서 선택합니다

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly.

### 1. tmux 확인

tmux가 설치되어 있는지 확인합니다:

```bash
which tmux
```

**IF 설치됨:** "✅ tmux가 이미 설치되어 있어요!"

**IF 미설치:**

"**tmux를 먼저 설치할게요!**

tmux는 터미널 세션을 유지해주는 도구예요.
스케줄러 데몬이 크론 결과를 Claude 세션에 전달하려면 tmux가 필요해요."

```bash
brew install tmux
```

설치 확인 후 진행합니다.

### 2. 현재 세션 확인

Claude가 tmux 안에서 실행 중인지 확인합니다:

```bash
echo $TMUX
```

**IF tmux 안에서 실행 중 ($TMUX 환경변수 있음):**

"✅ tmux 안에서 돌고 있어요!"

현재 tmux 세션 이름을 확인합니다:

```bash
tmux display-message -p '#S'
```

이 세션 이름이 스케줄러의 MAIN_SESSION 값이 됩니다.

**IF tmux 밖에서 실행 중:**

"**⚠️ 지금 tmux 밖에서 돌고 있어요!**

스케줄러가 크론 결과를 보내려면 Claude가 tmux 안에서 돌아야 해요.

지금 세션을 종료하고 tmux로 다시 시작해주세요:

```
tmux new-session -s [봇이름 또는 프로젝트명]
```

tmux 안에서 다시 Claude를 실행하면 자동으로 이어서 진행돼요!"

**중요:** tmux 세션 이름은 사용자에게 물어보세요. 프로젝트명이나 봇 이름을 기반으로 추천합니다.

여기서 사용자가 tmux 안에서 돌아올 때까지 기다립니다. 세션 종료가 필요하면 상태 파일을 업데이트하고 종료합니다.

### 3. 아키텍처 설명

"**스케줄러 데몬 구조를 설명해줄게요! 🏗️**

```
[Python 스케줄러 데몬] ← LaunchAgent로 항상 살아있음
  ├─ 30초마다 tick() → 시간 조건 체크
  ├─ 실행 방식:
  │   ├─ PY: Python 스크립트 직접 실행 — 빠름, 토큰 절약
  │   └─ AI: claude -p (별도 세션) — 판단/분석 필요할 때
  ├─ 응답 모드:
  │   ├─ silent: 조용히 처리, 로그만
  │   ├─ conditional: 새 데이터 있을 때만 메인 세션에 알림
  │   └─ notify: 항상 메인 세션에 알림
  └─ tmux 주입: 'Cron : {메시지}' 형태로 메인 세션에 전달
```

세션을 끄고 켜도, 데몬은 LaunchAgent로 항상 살아있어요!
크론 7일 만료 걱정도 없고, 재등록도 필요 없어요!"

### 4. scheduler_daemon.py 생성

프로젝트 루트 기준으로 `scripts/` 폴더가 없으면 생성합니다.

`scripts/scheduler_daemon.py` 파일을 생성합니다.

**중요: 프로젝트 경로, tmux 세션 이름, Claude 바이너리 경로를 모두 현재 환경에 맞게 설정합니다.**

경로 확인:
```bash
# 프로젝트 경로
pwd

# Claude 바이너리
which claude

# Python 바이너리
which python3

# tmux 바이너리
which tmux
```

생성할 `scripts/scheduler_daemon.py` 내용:

```python
#!/usr/bin/env python3
"""
[봇이름] 스케줄러 데몬
- Python이 스케줄링, Claude 메인 세션이 처리
- 모든 알림은 tmux send-keys로 메인 세션에 주입
- LaunchAgent로 백그라운드 실행 (자동시작)
"""
import subprocess
import json
import time
import threading
from datetime import datetime
from pathlib import Path

BOT_DIR = Path("[현재 프로젝트 절대 경로]")
MAIN_SESSION = "[tmux 세션 이름]"
STATUS_FILE = BOT_DIR / "dashboard/cron-status.json"
SCHEDULE_FILE = BOT_DIR / "dashboard/cron-schedule.json"
LOG_FILE = BOT_DIR / "memory/cron.log"
LOG_JSON_FILE = BOT_DIR / "dashboard/cron-log.json"

# ── 크론 메타데이터 ──────────────────────────────────────────
CRON_SCHEDULE = [
    {"key": "heartbeat", "name": "하트비트", "schedule": "4,34 * * * *", "method": "claude -p", "response": "silent", "desc": "대화 분석 · 파일 업데이트"},
]

# ── 기본 유틸 ──────────────────────────────────────────────

TMUX_BIN = "[tmux 절대 경로]"
CLAUDE_BIN = "[claude 절대 경로]"
PYTHON_BIN = "[python3 절대 경로]"

def tmux_inject(msg: str):
    """메인 세션에 프롬프트 주입 (Cron : 접두사 자동 추가)"""
    subprocess.run([TMUX_BIN, "send-keys", "-t", MAIN_SESSION, f"Cron : {msg}", "Enter"])

def run_script(*args, timeout=120) -> subprocess.CompletedProcess:
    """Python 스크립트 실행"""
    return subprocess.run(
        [PYTHON_BIN] + list(args),
        capture_output=True, text=True,
        cwd=BOT_DIR, timeout=timeout
    )

def claude_run(prompt: str, timeout=300):
    """claude -p로 새 세션 실행 (silent 작업용)"""
    subprocess.run(
        [CLAUDE_BIN, "--dangerously-skip-permissions", "-p", prompt],
        cwd=BOT_DIR, timeout=timeout,
        capture_output=True
    )

def log_status(name: str, status: str, detail: str = ""):
    """실행 결과 기록 — cron-status.json(최신), cron-log.json(이력), cron.log(텍스트)"""
    now = datetime.now()
    now_iso = now.isoformat()
    now_str = now.strftime("%Y-%m-%d %H:%M:%S")

    # 1. cron-status.json — 크론별 최신 상태
    try:
        data = json.loads(STATUS_FILE.read_text()) if STATUS_FILE.exists() else {"jobs": {}}
        data["jobs"][name] = {"status": status, "detail": detail, "last_run": now_iso}
        STATUS_FILE.write_text(json.dumps(data, ensure_ascii=False, indent=2))
    except Exception:
        pass

    # 2. cron-log.json — 최근 200개 이력
    try:
        log_data = json.loads(LOG_JSON_FILE.read_text()) if LOG_JSON_FILE.exists() else {"logs": []}
        log_data["logs"].append({"name": name, "status": status, "detail": detail, "ts": now_iso})
        log_data["logs"] = log_data["logs"][-200:]
        LOG_JSON_FILE.write_text(json.dumps(log_data, ensure_ascii=False, indent=2))
    except Exception:
        pass

    # 3. cron.log — 텍스트 로그 (append)
    try:
        detail_str = f" ({detail})" if detail else ""
        with open(LOG_FILE, "a") as f:
            f.write(f"[{now_str}] {name} — {status}{detail_str}\n")
    except Exception:
        pass


# ── 크론 함수들 ───────────────────────────────────────────

def heartbeat():
    """30분마다 — 대화 분석, 파일 업데이트 (silent)"""
    try:
        claude_run(
            "HEARTBEAT.md 읽고 처리해줘. 유저에게 메시지 보내지 말 것. HEARTBEAT_OK만 응답.",
            timeout=180
        )
        log_status("heartbeat", "ok")
    except Exception as e:
        log_status("heartbeat", "error", str(e))


# ── 스케줄러 ──────────────────────────────────────────────

class Scheduler:
    def __init__(self):
        self._ran = set()

    def _key(self, name: str) -> str:
        return f"{name}_{datetime.now().strftime('%Y%m%d%H%M')}"

    def run_once_per_minute(self, name: str, func):
        """같은 분에 중복 실행 방지"""
        key = self._key(name)
        if key in self._ran:
            return
        self._ran.add(key)
        if len(self._ran) > 1000:
            self._ran.clear()
        threading.Thread(target=func, daemon=True).start()

    def tick(self):
        now = datetime.now()
        m, h, wd, dom = now.minute, now.hour, now.weekday(), now.day

        # ── 하트비트: 4분, 34분 ──
        if m in (4, 34):
            self.run_once_per_minute("heartbeat", heartbeat)


def main():
    print(f"[{datetime.now()}] 스케줄러 시작")
    # dashboard 폴더 생성
    (BOT_DIR / "dashboard").mkdir(exist_ok=True)
    # 스케줄 메타데이터 저장 (대시보드용)
    SCHEDULE_FILE.write_text(json.dumps(CRON_SCHEDULE, ensure_ascii=False, indent=2))
    sched = Scheduler()
    while True:
        sched.tick()
        time.sleep(30)  # 30초마다 체크


if __name__ == "__main__":
    main()
```

**중요:** `[현재 프로젝트 절대 경로]`, `[tmux 세션 이름]`, `[tmux 절대 경로]`, `[claude 절대 경로]`, `[python3 절대 경로]`를 실제 값으로 교체해서 파일을 생성합니다. `which` 명령으로 확인한 경로를 사용하세요.

### 5. 필요 디렉토리 생성

```bash
mkdir -p dashboard
mkdir -p memory
```

### 6. LaunchAgent 생성

`~/Library/LaunchAgents/` 에 plist 파일을 생성합니다.

LaunchAgent 이름은 프로젝트에 맞게 설정합니다. 예: `com.[봇이름].scheduler`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>com.[봇이름].scheduler</string>
    <key>ProgramArguments</key>
    <array>
        <string>[python3 절대 경로]</string>
        <string>[프로젝트 절대 경로]/scripts/scheduler_daemon.py</string>
    </array>
    <key>WorkingDirectory</key>
    <string>[프로젝트 절대 경로]</string>
    <key>KeepAlive</key>
    <true/>
    <key>StandardOutPath</key>
    <string>[프로젝트 절대 경로]/memory/scheduler.log</string>
    <key>StandardErrorPath</key>
    <string>[프로젝트 절대 경로]/memory/scheduler.log</string>
</dict>
</plist>
```

**중요:** 모든 `[...]` 플레이스홀더를 실제 경로로 교체합니다.

### 7. 데몬 등록 및 시작

```bash
launchctl load ~/Library/LaunchAgents/com.[봇이름].scheduler.plist
```

### 8. 데몬 작동 확인

```bash
pgrep -f scheduler_daemon
```

**IF PID가 나옴:**

"✅ 스케줄러 데몬이 정상 실행 중이에요! (PID: [번호])

하트비트가 30분마다 자동으로 돌아갈 거예요.
다음 단계에서 추가 크론을 설정할 수 있어요!"

**IF PID가 안 나옴:**

로그를 확인합니다:

```bash
cat memory/scheduler.log
```

에러를 확인하고 사용자와 함께 해결합니다. 흔한 원인:
- Python 경로가 틀림
- 프로젝트 경로가 틀림
- 파일 권한 문제

### 9. scheduler-manage 스킬 설치

"**크론 관리 스킬도 설치할게요!**

나중에 크론을 추가/수정/삭제할 때 `/scheduler-manage`로 가이드를 받을 수 있어요."

init-claude-bot 레포의 `.claude/commands/scheduler-manage.md`를 프로젝트의 `.claude/commands/scheduler-manage.md`로 복사합니다.

```bash
mkdir -p .claude/commands
cp [init-claude-bot 경로]/.claude/commands/scheduler-manage.md .claude/commands/scheduler-manage.md
```

"✅ `/scheduler-manage` 스킬 설치 완료!"

### 10. 상태 업데이트

`{statusFile}`의 `stepsCompleted`에 `step-04a-daemon-setup`을 추가합니다.

### 11. Present MENU OPTIONS

Display: "**[C] 다음 단계로 진행 (크론 선택)**"

#### EXECUTION RULES:

- ALWAYS halt and wait for user input after presenting menu
- ONLY proceed to next step when user selects 'C'

#### Menu Handling Logic:

- IF C: `{statusFile}` 업데이트 후, `{nextStepFile}`을 로드하고, 전체를 읽은 후 실행합니다
- IF Any other: 사용자 질문에 답변 후 메뉴를 다시 표시합니다

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- tmux 설치 확인됨
- Claude가 tmux 안에서 실행 중임 확인됨
- scheduler_daemon.py 생성됨 (경로, 세션명 올바르게 설정)
- 하트비트 함수가 포함됨
- LaunchAgent plist 생성됨
- 데몬이 정상 실행됨 (pgrep으로 확인)
- scheduler-manage 스킬 설치됨
- 상태 파일 업데이트됨

### ❌ SYSTEM FAILURE:

- tmux 없이 진행
- tmux 밖에서 실행 중인데 안내 없이 진행
- 경로 플레이스홀더가 그대로 남아있음
- 데몬 실행 확인 없이 다음 스텝 진행
- 하트비트 누락
- 상태 파일 업데이트 누락

**Master Rule:** 데몬이 실제로 돌고 있음이 확인된 후에만 다음 스텝으로 진행해야 합니다. 하트비트는 필수입니다.
