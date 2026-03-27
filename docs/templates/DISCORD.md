# DISCORD.md - Discord Bot API 확장 기능

Discord MCP 플러그인으로 제공되지 않는 기능들을 Python 스크립트(`requests`)로 처리.

## 요구사항

- `pip install requests`
- 환경변수 `DISCORD_BOT_TOKEN` 설정 필요

## 사용 가능한 기능

### 프사/아바타 변경

```bash
python3 scripts/change_avatar.py path/to/image.jpg
```

### 봇 이름 변경

```python
import requests, os

token = os.environ["DISCORD_BOT_TOKEN"]
requests.patch(
    "https://discord.com/api/v10/users/@me",
    headers={"Authorization": f"Bot {token}", "Content-Type": "application/json"},
    json={"username": "새이름"}
)
```

### 임베드 메시지 전송

```python
import requests, os

token = os.environ["DISCORD_BOT_TOKEN"]
requests.post(
    f"https://discord.com/api/v10/channels/{CHANNEL_ID}/messages",
    headers={"Authorization": f"Bot {token}", "Content-Type": "application/json"},
    json={"embeds": [{"title": "제목", "description": "내용", "color": 65280}]}
)
```

## 주의사항

- 봇 토큰은 대화에 직접 붙여넣지 말고 환경변수로 관리
- 아바타 변경은 Discord API rate limit이 있음 (자주 변경 불가)
