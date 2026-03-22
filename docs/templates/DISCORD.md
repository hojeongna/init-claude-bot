# DISCORD.md - Discord Bot API 확장 기능

Discord MCP 플러그인으로 제공되지 않는 기능들을 Python 스크립트(`discord.py`)로 처리.

## 요구사항

- `pip install discord.py`
- 환경변수 `DISCORD_BOT_TOKEN` 설정 필요

## 사용 가능한 기능

### 프사/아바타 변경

```python
import discord, asyncio, os

async def change_avatar(image_path):
    token = os.environ["DISCORD_BOT_TOKEN"]
    client = discord.Client(intents=discord.Intents.default())

    @client.event
    async def on_ready():
        with open(image_path, "rb") as f:
            await client.user.edit(avatar=f.read())
        print("Avatar changed!")
        await client.close()

    await client.start(token)

asyncio.run(change_avatar("path/to/image.jpg"))
```

### 봇 이름 변경

```python
await client.user.edit(username="새이름")
```

### 봇 상태 메시지 설정

```python
activity = discord.Activity(type=discord.ActivityType.playing, name="게임 중")
await client.change_presence(activity=activity)
```

### 메시지 핀/언핀

```python
channel = client.get_channel(CHANNEL_ID)
message = await channel.fetch_message(MESSAGE_ID)
await message.pin()
```

### 임베드 메시지 전송

```python
embed = discord.Embed(title="제목", description="내용", color=0x00ff00)
embed.add_field(name="필드", value="값")
await channel.send(embed=embed)
```

## 주의사항

- `urllib` 사용 시 Cloudflare 차단됨 → 반드시 `discord.py` 또는 `aiohttp` 사용
- 봇 토큰은 대화에 직접 붙여넣지 말고 환경변수로 관리
- 아바타 변경은 Discord API rate limit이 있음 (자주 변경 불가)
