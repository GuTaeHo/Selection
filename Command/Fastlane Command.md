# Fastlane Command

fastlane 설치 및 초기화, 사용 방법과 자주 사용되는 명령어 정리

</br>


## 초기화

1. 프로젝트 루트 디렉토리로 이동 후 아래 명령어 수행

```bash
fastlane init
```

2. 배포 선택  
    테스트플라이트나 앱스토어 배포가 목적이므로 `2` or `3` 선택

3. 스킴 선택  
    (스킴이 여러개 일 경우) 원하는 스킴 선택

4. Apple ID or Apple Developer 로그인  
    ID, PW 입력 후 발급된 2차 인증 번호 (6자리) 입력

5. `App Store Connect team` 선택  
    계정이 여러 팀에 속해 있을 경우 표시됨

6. `Developer Portal` 의 team 선택
    5번과 동일

위 과정을 수행하면 `Appfile`, `Fastfile` 이 자동 추가된다.

`Appfile` 에는 앱 번들 ID, Apple ID, App Store Connect Team ID, Developer Protal Team ID 가 포함

`Fastfile` 에는 빌드 번호 증가, 앱 빌드, 테스트 플라이트 업로드가 포함된 lane 이 추가되어있다.

</br>

5. `beta` 로 된 lane 을 실행

```bash
fastlane beta
```

</br>

## 인증서 및 프로파일 설정

`archive` 된 앱을 `testflight` 에 업로드하기 위해서 배포 인증서와 프로비저닝 프로파일이 필요하다.  
만약 로컬 디렉토리에 저 파일이 없다면, fastlane 이 업로드에 실패한다.

`cert` 로 인증서를 생성 & 다운로드하고,  
`sign` 로 프로비저닝 프로파일을 생성 & 다운로드 한 뒤,

`testflight` 로 업로드 하려면 `lane` 에 아래 코드를 추가하면 된다

</br>

```ruby
lane :beta do
  # 인증서 생성/다운로드
  cert(
    development: false,
    username: "example@example.com" // Developer Portal Email 기입
  )

  # 프로비저닝 프로파일 생성/다운로드
  sigh(
    app_identifier: "com.bundle.com", // 앱 번들 ID 기입
    username: "example@example.com" // Developer Portal Email 기입
  )

  ... 

  build_app(workspace: "App.xcworkspace", scheme: "App")
  upload_to_testflight
end
```


</br>

## 업로드 오류해결

앱 빌드까지는 성공했지만, `upload_to_testflight` 에서  
"Failed to get authorization for username and password" 또는

"The call to the altool completed with a non-zero exit status: 1. This indicates a failure."

에러를 표시하며 실패할 수 있다.

</br>

TestFlight 업로드 처럼 App Store Connect 에 접근해야하는 작업에는 로그인이 반드시 필요한데,

Apple 은 Apple 이외의 개발자가 만든 서비스에서 Apple 계정 로그인을
제공하기 위해서 앱 암호 기능과 App Store Connect API 제공한다.

이럴경우 **앱 전용 비밀번호** 또는 **App Store Connect API** 를 이용할 수 있다.

</br>

### 앱 전용 비밀번호 생성

1. https://account.apple.com 에 로그인
2. 로그인 및 보안 > 앱 암호 > 앱 암호 생성
3. 암호 명과 계정 비밀번호 입력
4. 암호 복사 (이 암호는 다시 볼 수 없기 때문에 다른 곳에 미리 복사해 둘 것)
5. 환경 변수에 등록
    </br>

    **환경변수 등록 (세션)**
    ```bash
    export FASTLANE_USER="appleid@example.com"
    export FASTLANE_PASSWORD="생성된 암호"
    ```

    위 명령어는 터미널 **세션단위**로 실행된다.
    매 번 입력하기 귀찮을 경우 `~/.zshrc` 파일에 환경변수를 등록해주면 된다.

    </br>

    **환경변수 등록**

    ```bash
    vi ~/.zshrc

    ...

    export FASTLANE_USER="appleid@example.com"
    export FASTLANE_PASSWORD="생성된 암호"

    ...

    source ~/.zshrc
    ```

    </br>

6. 다시 lane 실행


</br>

### (권장) App Store Connect API 키 생성

1. App Store Connect 관리자 계정으로 로그인
2. 사용자 및 액세스 > 통합 > App Store Connect API 액세스 요청
3. API 키 생성 및 내용 입력
4. 생성된 키 다운로드 (한번만 가능하니 저장해둘 것)
5. fastlane 디렉토리에 생성된 키 복사
6. `lane` - `build_app()` 메소드 아래에 아래 소스 추가 (`upload_to_testflight` 메소드가 있다면 지울 것)
```ruby
api_key = app_store_connect_api_key(
    key_id: "AA1B22CDEF",
    issuer_id: "a1b12cde-1a12-1234-012a123c12cd",
    key_filepath: "fastlane/AuthKey_AA1B22CDEF.p8",
    duration: 1200,
    in_house: false
)

pilot(
    api_key: api_key,
    ipa: "#{ipa_path}/#{ipa_file_name}", # 업로드할 ipa 파일 위치
    changelog: "fastlane 테스트",
    skip_waiting_for_build_processing: true, # App Store Connect 업로드 완료까지 대기 스킵 유무
)
```

</br>

## .xcconfig 로 관리되는 프로젝트

앱 확장(App Extension) 을 구현하고 있는 앱이나 다양한 빌드 환경을 사용 중인 앱일 경우, 버전 동기화를 위해 `.xcconfig` 를 많이 사용할 것이다

App Version, Build Version 등을 xcconfig 에서 관리중일 경우, 
기존 `update_build_number` 와 `increment_build_number` 는 적합하지 않을 수 있는데

fastlane 이 Target > Build Settings 의 값을 .xcconfig 의 참조 값이 아닌 버전 상수(ex: 1.2.0)로 변경하기 떄문이다.

```bash
# 변경 전
Current Project Version = ${BUILD_VERSION}
Marketing Version = ${APP_VERSION}

# 변경 후
Current Project Version = 2
Marketing Version = 2.0.1
```

이렇게되면 .xcconfig 에서 **버전 관리가 동작하지 않게된다.**

이 때는 `.xcconfig` 파일을 읽어들여 버전을 수정하는 스크립트를 만들어 `lane` 이 실행될 때 호출해주면 된다

[XCConfigManager](#XCConfigManager) 에서는 `Key` 에 해당하는 `Value`를 업데이트 할 수 있는 스크립트가 있다.



</br>

## match

### 새로운 프로파일 생성 및 컴퓨터에 설치

1. fastlane 디렉토리 아래에 `Matchfile` 생성
2. `Matchfile` 에 아래 내용 작성

```bash
# git repository 주소 (https)
git_url("https://github.com/fastlane-match.git")
# git repository 주소 (ssh)
# git_url("git@github.com:/fastlane-match.git")

storage_mode("git")
# 선택 가능: appstore, adhoc, enterprise or development
# 개발용 프로파일 발급 시 "development", 앱 스토어 배포용 프로파일 발급시 "appstore"
type("development") 
username("test@example.com")  # 애플 이메일
```

</br>

### 주의사항

(2025/09/05 기준) 새로운 깃 레포지토리를 생성한뒤, 신규 프로파일 생성 시,  
인증서 접근을 보호하기 위한 `Passphrase` 를 요구한다,  
만약 공백으로 입력하면 `Git Repository` 에 인증서가 **업로드 되지**않는다

반드시 `Passphrase` 를 입력하도록하자

</br>

### 기존 프로파일을 만료하고 신규 프로파일 생성  

```bash
fastlane match nuke development # 기존 프로파일 제거
fastlane match development      # 신규 프로파일 생성
```

</br>

### 새로운 프로파일 컴퓨터에 설치

신규 프로파일 생성 시 **자동으로 설치**되지만, 만일 설치가 되지 않은경우 아래 명령 실행

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

### 기존 프로파일에 신규 기기 등록

1. Apple Developer 사이트 접속
2. Account > Certificates, Identifiers & Profiles > **Devices** 탭 클릭
3. `+` 클릭 후, 등록될 신규 기기의 플랫폼과 디바이스 명, 디바이스 ID 추가  
  디바이스 ID 는 Xcode 에 등록될 기기 연결 후,  
  Xcode > Product > Destinations > Manage Run Destinations 의  
  Identifier 뒷자리 (ex: `00001234-001234060A2300BC`) 를 넣어주면 된다
4. 완료 후 **Profiles** 탭 클릭 후, 기존 프로파일 클릭
5. `edit` 클릭 후, 방금 등록된 디바이스 선택 후 `save`
6. 아래 명령 수행 (수정된 프로파일 다운로드)

```bash
fastlane match development
```

</br>
프로파일이 수정되면서, `fastlane` 은 기존 인증서가 만료됐음을 인지하고 갱신된 인증서를 자동으로 내려받는다

</br>

`nuke` 는 프로파일을 완전히 제거 후, 새롭게 프로파일을 생성하는 반면,  

`--force` 는 프로파일을 유지한 채로 갱신한다는 차이점이 있다.

`nuke` 는 기존 프로파일이 **완전히 제거**되어 동일한 프로파일을 사용하는 팀 전체에 영향이 갈 수 있기 때문에 주의해야한다.

</br>
</br>

## CI(Git Action & Runner) 환경에서 Match 로 프로파일 가져오기

`https` 의 인증오류 발생과 디바이스 지정 등 까다로운 설정때문에, ci 환경에서 사용하기 용이한 `ssh` 인증을 사용하였다.

아래는 `ssh` 로 match 프로파일을 가져오는 방법이다.

</br>

1. MatchFile 의 레포지토리 주소를 `ssh` 형태로 설정한다.
2. FastFile 에 match `git_url` 을 `ssh` 형태로 설정한다.
3. match 프로파일이 저장된 repository 의 settings 로 이동한다.
4. 로컬 환경에 `ssh-keygen -t ed25519 -C "example.com"` 명령으로 ssh 공개&개인 키를 생성한다.
5. `Add deploy key` 를 누르고 생성된 .pub 키를 저장한다.
6. match 프로파일이 저장된 repository 의 `Secrets and variables` 로 이동해 생성된 개인키(ex: MATCH_SSH_KEY)를 추가한다.
7. `Workflow` 에서 러너에 ssh 키 등록 & fastlane 을 실행한다
  ```yaml
  # ssh 키 등록
  - name: Set up SSH key for match
    run: |
      mkdir -p ~/.ssh
      echo "${{ secrets.MATCH_SSH_KEY }}" > ~/.ssh/id_ed25519
      chmod 600 ~/.ssh/id_ed25519
      ssh-keyscan github.com >> ~/.ssh/known_hosts
    shell: bash

  # fastlane 실행
  - name: Run Fastlane
    env:
      MATCH_PASSWORD: ${{ secrets.MATCH_PASSWORD }}
    run: |
      if [ "${GITHUB_REF##*/}" = "main" ]; then
        bundle exec fastlane release
      else
        bundle exec fastlane beta
      fi
  ```

</br>

### 주의사항

#### 인증 오류

`match` 시 프로파일을 git clone 을 통해 가져올 때, 인증 오류가 발생할가능성이 있다.

GUI 가 없는 CI 환경에서 `https` 로 인증 시 발생한 여러 문제점(디바이스 지정 안됨 및 클론 불가)으로 인해 `ssh` 인증을 권장한다

</br>

#### Github Action 권한

private repo 의 경우 `Personal Access Token(PAT)` 가 반드시 필요,  
PAT 에는 최소 `repo` 권한이 포함되어야함

</br>
</br>


## 참고

### XCConfigManager

```ruby
class XCConfigManager 
  # xcconfig 파일 경로 (기본값: "../Config/Shared.xcconfig")
  def initialize(path = "../Config/Shared.xcconfig")
    @path = path
  end

  def get_xcconfig_value(key)
    lines = File.readlines("#{@path}")

    annotation_removed_lines = lines.compact do |line|
      # 주석 라인 제거
      if !line.strip.start_with?("//")
        line
      end
    end

    value = annotation_removed_lines.find { |line| line.strip.start_with?("#{key}") }
    return value.gsub(/\s+/, "").split("=")[1]
  end

  def update_xcconfig(key, new_value)
    lines = File.readlines("#{@path}")

    updated_lines = lines.map do |line|
      if line.strip.start_with?("#{key} =")
        "#{key} = #{new_value}\n"
      else
        line
      end
    end

    File.write("#{@path}", updated_lines.join)

    puts "\"#{key}\" 업데이트: #{get_xcconfig_value(key)}"
  end


=begin
  App Version 을 증가시킵니다

  - Parameters: type = 0 (major), 1 = (minor), 2 = (patch)
  - Note: 상위 버전이 증가될 경우, 하위 버전은 0으로 초기화 됩니다. (ex: 1.2.2 -> 1.3.0)
  - Important: 호출 시, 빌드 버전은 항상 `0` 으로 초기화 됩니다
=end
  def increment_app_version(type)
    app_version = get_xcconfig_value("APP_VERSION")

    major_version = app_version.split(".")[0].to_i
    minor_version = app_version.split(".")[1].to_i
    patch_version = app_version.split(".")[2].to_i
    new_version = ""

    if type == 0
      new_version = "#{major_version + 1}" + "." + "0" + "." + "0"
    elsif type == 1
      new_version = "#{major_version}" + "." + "#{minor_version + 1}" + "." + "0"
    else
      new_version = "#{major_version}" + "." + "#{minor_version}" + "." + "#{patch_version + 1}"
    end

    update_xcconfig("APP_VERSION", new_version)
    update_xcconfig("BUILD_VERSION", 0)
  end
end
```