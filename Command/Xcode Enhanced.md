# Xcode Enhanced
Xcode 를 조금 더 생산적이며 효율적으로 사용할 수 있는 방법 정리

<br>

## 단축키
### 
```swift
// 현재 화면 새로운 탭으로 열기
Command + t

// 현재 파일 네비게이터에 표시
Command + Shift + j

// 상단 바 고정
Alt + Command + t
```

<br>

## 빌드
### 빌드 시간 표시
```
defaults write com.apple.dt.Xcode ShowBuildOperationDuration -bool YES
rm -rf ~/Library/Developer/Xcode/DerivedData
``` 

위 명령 수행 후, Run Device 선택 창에 빌드 시간 표시됨

<br>

## 디버깅

### 디버그 연결중단 개선

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

<br>

### 디버깅 속도 개선

1. 홈 디렉토리에 .lldbinit 파일을 생성한다.
```bash
vi ~/.lldbinit
```

2. 다음 라인을 파일에 추가한다.  
```bash
settings set target.experimental.swift-enable-cxx-interop false
```

3. Xcode 를 재실행한다.

<br>

## Git

### Git ssh 연결
1. 키 생성 명령어 입력
```bash
ssh-keygen -t ecdsa -C "your_email@example.com" -m PEM
```

![허용되지 않은 키 에러](../Resource/Image/Command/imgXcodeNotAllowSha1.png)  

*keygen 의 타입 옵션을 주지 않으면 SHA-1 으로 생성된다.  
Xcode 는 SHA-1 으로 암호화된 키를 허용하지 않기 때문에 ecdsa 방식으로 생성해야한다*

*PEM 옵션은 pull, push 를 수행할 때 마다 인증을 해줘야하는 번거로움을 제거한다*

2. ssh 키 생성  
~/.ssh 경로확인 및 passphrase 를 엔터로 스킵한다.   
![키 생성](../Resource/Image/Command/imgGeneratedSSHKeys.png)  

3. 공개키(.pub) 깃 허브에 저장  
프로필 > Settings > SSH and GPG keys > New SSH key  
![키 등록](../Resource/Image/Command/imgGitSettings.png)  

4. Xcode Git 설정  
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

## Swift Package

### SPM 패키지 캐시 완전히 날려버리는 방법
```
rm -rf ~/Library/Developer/Xcode/DerivedData
rm -rf ~/Library/Caches/org.swift.swiftpm
rm -rf ~/Library/org.swift.swiftpm
rm -rf ~/.swiftpm
```

<br>

### Package 추가 시 에러가 발생할 때
> Xcode > File > Add Package Dependencies 후 설치 시 아래 에러 발생  
> 에러 내용: skipping cache due to an error the repository could not be found

<br>

**해결 방법 1)**
- DerivedData 를 날리고 재시도한다.

**해결 방법 2)**
- Xcode 를 완전히 종료한 다음 아래 명령어를 실행한다

    ```bash
    defaults write com.apple.dt.Xcode IDEPackageSupportUseBuiltinSCM YES
    ```

<br>

### 높은 버전 Package 가 내려받아지지 않을 때
> branch 를 지정한 패키지의 높은 버전이 Git 에 올라와 있지만,  
> 프로젝트에서 이전 버전의 버전만 보고있음

<br>

**해결 방법 1)**
1. Xcode > File > Packages > Update to Last Package Versions 실행
2. **Xcode 를 완전히 끄고 다시 실행**

<br>

### 참고  

[lldb 가 왜 이렇게 느리죠?](https://stackoverflow.com/questions/75850606/why-is-lldb-so-painfully-slow)

[디버거 연결 해제](https://forums.developer.apple.com/forums/thread/681037)

[git push 를 할 때 마다 인증 요청 문제](https://stackoverflow.com/questions/53879986/xcode-10-1-push-to-github-using-ssh-key) 