# Xcode Enhanced
Xcode 를 조금 더 생산적이며 효율적으로 사용할 수 있는 방법 정리

<br>

**디버그 연결중단 개선**

![Debugger lost connection](../Resource/Image/Command/imgLostConnectionDebugger.png)

1. 홈 디렉토리에 .lldbinit 파일을 생성한다.
```bash
vi ~/.lldbinit
```
2. 다음 라인을 파일에 추가한다.
```bash
settings set plugin.process.gdb-remote.packet-timeout 300
```
3. Xcode 를 재실행한다.

참고  
[Lost connection to the debugger - apple forum](https://forums.developer.apple.com/forums/thread/681037)

<br>

**디버깅 속도 개선**
```bash
vi ~/.lldbinit
```
```bash
settings set target.experimental.swift-enable-cxx-interop false
```

<br>

**Git ssh**
1. 키 생성 명령어 입력
```bash
ssh-keygen -t ecdsa -C "your_email@example.com"
```

![허용되지 않은 키 에러](../Resource/Image/Command/imgXcodeNotAllowSha1.png)  

*keygen 의 타입 옵션을 주지 않으면 SHA-1 으로 생성된다.  
Xcode 는 SHA-1 으로 암호화된 키를 허용하지 않기 때문에 ecdsa 방식으로 생성해야한다*

2. ssh 키 생성  
~/.ssh 경로확인 및 passphrase 를 엔터로 스킵한다.   
![키 생성](../Resource/Image/Command/imgGeneratedSSHKeys.png)  

3. 공개키(.pub) 깃 허브에 저장
프로필 > Settings > SSH and GPG keys > New SSH key  
![키 등록](../Resource/Image/Command/imgGitSettings.png)  

4. Xcode 설정  
Xcode > Settings > Accounts > Source Control Accounts  
![Xcode 키 등록](../Resource/Image/Command/imgSSHKeyRegistForXcode.png)  

5. 원격지 변경
http 로 clone 을 받은 프로젝트라면 ssh 로 변경해줘야한다.  
```bash
# 원격지 확인
git remote -v

# 원격지 설정
git remote set-url origin git@github.com:<RepoName>/<RepoName>.git
```

<br>

참고  
[why lldb so painfully slow - stackoverflow](https://stackoverflow.com/questions/75850606/why-is-lldb-so-painfully-slow)
