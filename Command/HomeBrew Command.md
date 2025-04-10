# Home Brew 명령어



##### 패키지 업데이트

```bash
brew update         # 패키지 최신정보 가져옴
brew outdated       # 현재 설치된 패키지 중 업데이트 가능한 패키지 표시
brew upgrade        # outdated 명령에서 나열된 패키지 업그레이드
```

##### 패키지 설치

```bash
brew install <패키지명>
# GUI 프로그램의 경우
brew install --cask <패키지명>
```

##### 패키지 확인

```bash
brew search <패키지명>  # 패키지 있는지 검색
brew list             # 설치된 패키지 목록 표시 (= brew ls)
brew info <패키지명>    # 패키지 정보 표시
```

##### 패키지 삭제

```bash
brew cleanup <패키지명>    # 해당 패키지의 최신버전을 제외한 버전 모두 삭제
brew uninstall <패키지명>  # 해당 패키지 삭제
```

