# Git Command

## git config

깃 은 여러 사용자 편의성을 위해 config 를 지원하는데, 크게 3종류로 구분한다

`system` 은 해당 컴퓨터에 있는 모든 저장소에 적용된다. **저장 위치 (유닉스 기준): /etc/gitconfig**

`global` 은 운영체제 사용자 폴더에 있는 모든 저장소에 적용된다. **저장 위치: ~/.gitconfig**

`local` 은 저장소에 한정된다. **저장 위치: .git/config**

</br>

**주의사항**
config 에 설정되는 **user.name** 은 `GitHub`의 사용자 이름이 **아니라** 
깃 커밋 시 사용될 사용자 이름이다 혼동하지 않도록 유의

</br>

### git config 읽기

```bash
git config --list # system, global, local 모두 표시
git config --global --list # global 만 표시

git config <name> # 설정된 이름 표시
# ex) git config user.name
```

</br>

### git config 쓰기

범위 옵션을 주지 않을 경우 `local` 을 기준으로 값이 설정된다


```bash
git config user.name <name> # 유저 명 설정
git config user.email <email> # 유저 이메일 설정
# ex) git config user.email example.com


git config --global user.name <name> # global 유저 명 설정
# ex) git config --global user.name gutaeho

```

</br>

### git config 지우기

범위 옵션을 주지 않을 경우 `local` 을 기준으로 값이 설정된다

```bash
git config --unset user.name # 유저 명 제거 
# ex) git config --unset --global user.name gutaeho

git config --unset --global user.name # global 유저 명 제거 
# ex) git config --unset --global user.name gutaeho
```

</br>

### 한글이 "\123" 형식으로 표시될 때

한글은 git 에서 **일반적이지 않은(unusual) 문자**로 인식

```bash
git config --global core.quotepath false
```

</br>
</br>

## git clone

### Git Credential Helper (= Credential Manager)

HTTPS 인증을 통해 클론을 수행할 때 깃은 자격 증명을 요구한다.
클론에 성공하고, 디렉토리를 지우고 새로 클론할 때, 다시 인증 요청을 하지않고
**즉시 클론** 되는 것을 볼 수 있는데, 이는 macOS/Linux 의 `Git Credential Helper` 때문이다.
(Windows 는 `Git Credential Manager`)

한 번 인증하는 순간 토큰 or 비밀번호가 저장되어 인증 요구가 없어진다.

인증 정보는 `macOS` 의 경우 **키체인**에, `Linux` 의 경우 일정시간동안(기본값: 15분)만 
유지되는 메모리에 저장된다.

</br>

**확인**

```bash
git config --list
```

(macOS 기준) 위 명령 수행 및 **credential.helper=osxkeychain** 항목이 있을경우,
키체인에 이미 저장된 인증 정보가 있을 것이다.

**결과**
![Credential Helper](../Resource/Image/Command/imgGitCloneCredentialHelper.png)


</br>
</br>

## git push

### Tag 와 함께 현재 브랜치 푸쉬

```bash
git push origin main {태그명}
# ex) git push origin main v2.0.1
```

</br>
</br>

## git fetch

### Remote 에서 제거된 Local 브랜치 제거하기

```bash
git fetch --prune
``` 

</br>
</br>

## git stash

### 특정 파일만 스태시하기

```bash
git stash push -m "스태시 명" [파일명]
# git stash push -m "Test: 결제 수단 강제 오픈" ~/example/MainViewController.swift
```

</br>
</br>

## git restore

### 스테이지된 모든 파일 내리기

```bash
git restore --staged .
```

</br>

## git remote

### 보이지 않는 원격지 브랜치 갱신

원격지에 새로 생성된 브랜치가 있고, `git pull` 를 통해 로컬에 가져오려고 했지만 `git branch` 로 확인 해 봐도 브랜치가 보이지 않았다.  
원격지의 변경사항을 확인하기전엔 **git remote** 명령을 통해 원격지의 변경사항을 먼저 가져와야한다.

```bash
git remote update
// 그리고
git pull
```

</br>

### 현재 연결된 remote 출력

```bash
git remote -v
```

</br>

### Git 원격지 두 곳에 동시에 브랜치를 push 하는 방법

github 와 gitlab 의 각각의 repository 가 있고,  
한 번의 push 명령으로 두 브랜치 모두 적용하고 싶을 때

```bash
# 기존 원격 주소 연결 및 별칭 추가
git remote origin https://github.com/user/repository.git
# 새로운 원격 주소 추가
git remote set-url --add --push origin https://github.com/user/repository.git
# 브랜치 푸시
git push origin
```

</br>

**주의사항**:

1. 맨 처음 set-url 옵션으로 url 을 지정할 경우, 기존에 있던 URL 이 대체되어버린다, 그럴 떄는 기존 url 을 한번더 set-url 로 지정하면 된다.

2. `Xcode` 상에서 `push` 할 경우 특정 저장소에만 푸시되는걸로 보이는데, 터미널 상에서 `git push origin` 명령을 사용하면 동시에 푸시된다.

</br>
</br>

## git rm

### 원격 저장소와 로컬 저장소에 있는 파일을 삭제

```bash
git rm [File Name]
```

</br>

### 로컬 저장소에 있는 파일은 그대로 두고, 원격 저장소에 있는 파일을 삭제

```bash
git rm --cached [File Name]
# App 디렉토리 아래의 AppDelegate.swift 파일 삭제 예시
# ex) git rm --cached App/AppDelegate.swift
# App 디렉토리 아래의 모든 파일 삭제
# ex) git rm --cached -r App/
```

</br>

### .DS_Store 파일이 깃에 포함되는 문제

1. 하위 모든 디렉토리의 .DS_Store 파일 일괄 제거

```bash
find . -name .DS_Store -print0 | xargs -0 git rm -f --ignore-unmatch
```

2. 이미 원격지에 커밋되었다면 제거 후 다시 커밋

```bash
git rm -r --cached .    // 기존에 작성된 내역을 제거 (실제 파일은 제거되지 않으니 안심)
git add .
git commit -m "applying .gitignore"
```

3. .gitignore 파일 생성 후 아래 내용 추가

```vim
.DS_Store
._.DS_Store
**/.DS_Store
**/._.DS_Store
```

</br>
</br>

## git reflog

### 가장 최근 커밋 부터 커밋 태그 및 메시지를 표시한다

```bash
git reflog
```  

</br>
</br>

## git log

### 특정 파일의 변경 로그 확인

### 문자열과 일치하는 커밋 확인

```bash
git log -S function_name
# ex) git log -S AppDelegate
```

<br>

### 문자열과 일치하는 커밋의 내용과 함께 확인

```bash
git log -S function_name -p
# ex) git log -S AppDelegate -p
```

</br>
</br>

## git ignore

### Configs 폴더를 유지하면서, 아래 폴더와 파일은 커밋하지 않는 방법

```bash
# Configs 디렉토리 하위 모든 파일 무시
Configs/* 
# Configs 디렉토리만 추적
!Configs/

# 빈 폴더는 git 에서 추적하지 않음, .gitkeep 파일 생성 후 Configs 디렉토리 추적
touch Configs/.gitkeep
!Configs/.gitkeep (.gitkeep 파일은 추적함)
```
