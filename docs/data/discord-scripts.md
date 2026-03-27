# Discord 스크립트 레퍼런스

## fetch_discord.py

`scripts/fetch_discord.py`에 생성할 코드:

```python
#!/usr/bin/env python3
"""디스코드 최근 채팅 내역을 불러오는 스크립트."""

import requests
import argparse
import os
from datetime import datetime, timezone


def fetch_messages(channel_id: str, limit: int = 20):
    token = os.environ.get("DISCORD_BOT_TOKEN")
    if not token:
        print("ERROR: DISCORD_BOT_TOKEN 환경변수가 설정되지 않았습니다.")
        return

    resp = requests.get(
        f"https://discord.com/api/v10/channels/{channel_id}/messages?limit={limit}",
        headers={"Authorization": f"Bot {token}"},
        timeout=10,
    )
    resp.raise_for_status()
    messages = list(reversed(resp.json()))

    print(f"## 최근 대화 (채널: {channel_id})")
    print()
    for msg in messages:
        ts = datetime.fromisoformat(msg["timestamp"].replace("Z", "+00:00"))
        timestamp = ts.astimezone(timezone.utc).strftime("%Y-%m-%d %H:%M")
        author = msg["author"].get("global_name") or msg["author"]["username"]
        content = msg.get("content", "")
        if not content and msg.get("embeds"):
            content = " | ".join(
                e.get("description") or e.get("title") or ""
                for e in msg["embeds"]
                if e.get("description") or e.get("title")
            )
        print(f"[{timestamp}] {author}: {content}")
        for att in msg.get("attachments", []):
            print(f"  📎 {att['filename']} ({att.get('content_type', '')})")


def main():
    parser = argparse.ArgumentParser(description="디스코드 최근 채팅 불러오기")
    parser.add_argument("--limit", type=int, default=20, help="불러올 메시지 수 (기본: 20)")
    parser.add_argument("--channel", type=str, help="채널 ID (필수)")
    args = parser.parse_args()
    if not args.channel:
        print("ERROR: --channel 인자가 필요합니다.")
        return
    fetch_messages(args.channel, args.limit)


if __name__ == "__main__":
    main()
```

**의존성:** `pip install requests` (discord.py 불필요)

**사용법:**
```bash
python3 scripts/fetch_discord.py --channel <채널ID> --limit 20
```

---

## change_avatar.py

`scripts/change_avatar.py`에 생성할 코드:

```python
#!/usr/bin/env python3
"""디스코드 봇 아바타를 변경하는 스크립트."""

import requests
import base64
import sys
import os


def change_avatar(image_path: str):
    token = os.environ.get("DISCORD_BOT_TOKEN")
    if not token:
        print("ERROR: DISCORD_BOT_TOKEN 환경변수가 설정되지 않았습니다.")
        return

    with open(image_path, "rb") as f:
        image_data = base64.b64encode(f.read()).decode("utf-8")

    ext = image_path.rsplit(".", 1)[-1].lower()
    mime = {"png": "image/png", "jpg": "image/jpeg", "jpeg": "image/jpeg", "gif": "image/gif"}.get(ext, "image/png")

    resp = requests.patch(
        "https://discord.com/api/v10/users/@me",
        headers={"Authorization": f"Bot {token}", "Content-Type": "application/json"},
        json={"avatar": f"data:{mime};base64,{image_data}"},
        timeout=10,
    )
    resp.raise_for_status()
    print(f"아바타 변경 완료! ({image_path})")


if __name__ == "__main__":
    if len(sys.argv) < 2:
        print("사용법: python3 scripts/change_avatar.py <이미지경로>")
        sys.exit(1)
    change_avatar(sys.argv[1])
```

**의존성:** `pip install requests` (discord.py 불필요)
