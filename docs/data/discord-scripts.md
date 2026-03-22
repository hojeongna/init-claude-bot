# Discord 스크립트 레퍼런스

## fetch_discord.py

`scripts/fetch_discord.py`에 생성할 코드:

```python
#!/usr/bin/env python3
"""디스코드 최근 채팅 내역을 불러오는 스크립트."""

import discord
import asyncio
import argparse
import os


async def fetch_messages(limit: int = 20):
    token = os.environ.get("DISCORD_BOT_TOKEN")
    if not token:
        print("ERROR: DISCORD_BOT_TOKEN 환경변수가 설정되지 않았습니다.")
        return

    intents = discord.Intents.default()
    intents.message_content = True
    client = discord.Client(intents=intents)

    @client.event
    async def on_ready():
        # 봇이 접근 가능한 첫 번째 텍스트 채널에서 메시지 수집
        for guild in client.guilds:
            for channel in guild.text_channels:
                try:
                    messages = []
                    async for msg in channel.history(limit=limit):
                        messages.append(msg)
                    messages.reverse()

                    print(f"## 최근 대화 ({channel.name})")
                    print()
                    for msg in messages:
                        timestamp = msg.created_at.strftime("%Y-%m-%d %H:%M")
                        print(f"[{timestamp}] {msg.author.display_name}: {msg.content}")
                        if msg.attachments:
                            for att in msg.attachments:
                                print(f"  📎 {att.filename} ({att.content_type})")
                    break  # 첫 번째 채널만
                except discord.Forbidden:
                    continue
            break  # 첫 번째 서버만
        await client.close()

    await client.start(token)


def main():
    parser = argparse.ArgumentParser(description="디스코드 최근 채팅 불러오기")
    parser.add_argument("--limit", type=int, default=20, help="불러올 메시지 수 (기본: 20)")
    args = parser.parse_args()
    asyncio.run(fetch_messages(args.limit))


if __name__ == "__main__":
    main()
```

---

## change_avatar.py

`scripts/change_avatar.py`에 생성할 코드:

```python
#!/usr/bin/env python3
"""디스코드 봇 아바타를 변경하는 스크립트."""

import discord
import asyncio
import sys
import os


async def change_avatar(image_path: str):
    token = os.environ.get("DISCORD_BOT_TOKEN")
    if not token:
        print("ERROR: DISCORD_BOT_TOKEN 환경변수가 설정되지 않았습니다.")
        return

    client = discord.Client(intents=discord.Intents.default())

    @client.event
    async def on_ready():
        with open(image_path, "rb") as f:
            await client.user.edit(avatar=f.read())
        print(f"아바타 변경 완료! ({image_path})")
        await client.close()

    await client.start(token)


if __name__ == "__main__":
    if len(sys.argv) < 2:
        print("사용법: python3 scripts/change_avatar.py <이미지경로>")
        sys.exit(1)
    asyncio.run(change_avatar(sys.argv[1]))
```
