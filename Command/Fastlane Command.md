# Fastlane Command

fastlane 에서 자주 사용되는 명령어 정리

</br>

## match

### 새로운 프로파일 생성 및 컴퓨터에 설치

1. fastlane 디렉토리 아래에 `Matchfile` 생성
2. `Matchfile` 에 아래 내용 작성

```bash
git_url("https://githun.com/fastlane-match.git") # git repository 주소
storage_mode("git")
# 선택 가능: appstore, adhoc, enterprise or development
# 개발용 프로파일 발급 시 "development", 앱 스토어 배포용 프로파일 발급시 "appstore"
type("development") 
username("test@example.com")  # 애플 이메일
```

1. 기존 프로파일을 만료하고 신규 프로파일을 생성  

```bash
fastlane match nuke development # 기존 프로파일 제거
fastlane match development      # 신규 프로파일 생성
```

1. 새로운 프로파일 컴퓨터에 설치

> 신규 프로파일 생성 시 **자동으로 설치**되지만, 만일 설치가 되지 않은 경우 아래 명령 실행

```bash
fastlane match development --readonly
```

</br>

### 기존 프로파일을 컴퓨터에 설치

```bash
fastlane match development --readonly   # 개발용
fastlane match appstore --readonly      # 배포용
```

<br>

### 프로파일에 새로운 기기 등록
```bash
fastlane match development --force
```

<br>

> `nuke` 는 프로파일을 완전히 제거 후, 새롭게 프로파일을 생성하는 반면,  
>
> `--force` 는 프로파일을 유지한 채로 갱신에 의의가 있다.
>
> `nuke` 는 기존 프로파일이 **완전히 제거**되어 동일한 프로파일을 사용하는 팀 전체에 영향이 갈 수 있기 때문에 주의해야한다.

<br>
