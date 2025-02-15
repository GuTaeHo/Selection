# fastlane 적용기
토이 프로젝트에 fastlane 을 통한 빌드 & 배포 자동화 적용기이다.  
명령 수행시 권한 문제가 발생한다면 **sudo** 명령을 통해 진행하면 된다.


<br>

## fastlane 패키지 설치
첫 번째로 brew 를 이용한 fastlane 패키지를 설치해준다.

```bash
brew install fastlane
```

<br>

다음으로 bundler 패키지를 설치해준다. bundler 는 gem 패키지 관리자를 통해 설치한다.
> 번들러가 설치된 경우 fastlane 을 업데이트할 때 `bundle update` 명령만 사용해주면 된다. 

```bash
gem install bundler
```

<br>
<br>

## 프로젝트 초기화
fastlane을 설치하고자 하는 프로젝트에서 다음 명령을 실행한다.

```bash
fastlane init
```

선택 화면이 표시되면 파일을 직접 설정하기 위해 **4번(Manual Setup)** 을 입력한다.

<br>

https://appleid.apple.com/ 에서 애플로그인을 한 뒤,  
로그인 및 보안 > 앱 암호 에서 앱 암호를 발급 받는다.
> 이 앱 암호는 fastlane 에서 애플 서버에 접근할 때 매번 비밀번호와 이중인증을 스킵할 수 있도록 해준다.

<br>
<br>

앱 스토어 커넥트와 fastlane 간 인증 방식에는 **Cert, Sigh** 와 **Match** 크게 두 가지 방식이 있는데,  
지금은 **Cert, Sigh** 로 진행하도록 하겠다.

<br>
<br>

### Appfile 설정
1. 프로젝트 디렉토리에서 `fastlane/Appfile` 에 들어가서 아래처럼 변경한다.

```
app_identifier("자신의 앱 identifier") # The bundle identifier of your app
apple_id(ENV["APPLE_ID"]) # Your Apple email address -> Please Set Env Variable in /fastlane/.env!!
```

<br>


2. `fastlane` 디렉토리 아래에 .env 파일을 생성하고, 인증 정보를 작성한다.

```zsh
vi fastlane/.env

... 

APPLE_ID="자신의 애플 아이디"
FASTLANE_PASSWORD="자신의 애플 비밀번호"
FASTLANE_APPLE_APPLICATION_SPECIFIC_PASSWORD="앱 암호"
```

<br>

3. 프로젝트 디렉토리에서 .gitignore 파일을 열고 환경변수가 지정된 파일을 입력해준다.
```
fastlane/.env
```

<br>
<br>

### Fastfile 설정 (Testfilght 에 배포)
1. `fastlane` 디렉토리 아래에 fastfile 을 열고 아래와 같이 작성한다.

```ruby
  desc "build app and upload to testflight"
  lane :beta do     // 1
    get_certificates        // 2
    get_provisioning_profile        // 3
    increment_build_number(
        build_number: latest_testflight_build_number + 1    // 4
    )
    build_app(
        scheme: "AppScheme"     // 5
        configuration: "Debug",    // 6
    )
    upload_to_testflight(    // 7
        skip_waiting_for_build_processing: true,     // 8
        changelog: "what's new"   // 9
    )
  end
```

<br>

**설명**  
1. lane 지정, `fastlane beta` 명령을 치면, 해당 lane 이 수행된다.
2. Cert, Sigh 방식으로 인증할 때 사용하는 인증 함수다. get_certificates로 인증서를, get_provisioning_profile로 프로필을 가져온다. 여기서 문제가 생기면 애플 계정 로그인이 뭔가 잘못되었거나, 인증서가 안만들어져있다든가 하는 문제점이 있을 수 있으니 **인증 상태가 제대로 되어있는지 확인**해보자.
3. 2와 동일
4. 빌드 넘버를 올린다, 로컬 파일에도 적용되며, 최근에 Testfilght 에 올라온 빌드에 1을 더해서 올린다
5. 빌드 시 어떤 scheme 으로 빌드할지 선택이 가능하다. (ex: myapp-debug)
6. 빌드 시 어떤 Configuration 로 빌드할지 선택이 가능하다. `Debug`, `Release` 혹은 추가적인 빌드 구성에 맞게 지정하면 된다. 
7. `TestFlight` 로 앱을 업로드한다.
8. 업로드 후 App Store Connect 에 올라가기 전까지 시간이 걸리는데 스킵할 경우 true 로 설정
9. 이 빌드에서 테스트할 사항 전달 시 설정 

<br>

#### + 업로드 중 에러 알림 설정


<br>
<br>




## fastlane 팁 명령어

**인자 목록 출력**

ex) 앱 빌드(build_app)
```
fastlane action build_app
```

ex) 테스트 플라이트 업로드(upload_to_testflight)
```
fastlane action upload_to_testflight
```