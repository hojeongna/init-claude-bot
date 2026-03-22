# 환경변수 설정 가이드

봇 토큰을 환경변수로 설정하는 방법입니다. OS에 맞는 방법을 따라하세요.

## Windows

Windows 설정에서:
1. 시작 메뉴에서 **'환경 변수'** 검색
2. **'시스템 환경 변수 편집(Edit the system environment variables)'** 클릭
3. **환경 변수(Environment Variables)** 버튼 클릭
4. **사용자 변수(User variables)**에서 **새로 만들기(New)** 클릭
5. 변수 이름: `DISCORD_BOT_TOKEN` (또는 `TELEGRAM_BOT_TOKEN`)
6. 변수 값: 복사해둔 토큰 붙여넣기
7. **확인(OK)** 클릭

또는 PowerShell로:
```powershell
[Environment]::SetEnvironmentVariable('DISCORD_BOT_TOKEN', '여기에_토큰_붙여넣기', 'User')
```

## macOS/Linux

셸 설정 파일에 추가:
```bash
echo 'export DISCORD_BOT_TOKEN="여기에_토큰_붙여넣기"' >> ~/.bashrc
source ~/.bashrc
```
(zsh 사용자는 `~/.zshrc`에 추가)
