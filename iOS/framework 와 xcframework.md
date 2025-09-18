# framework 와 xcframework

기존 `library` 는 소스코드만 포함이 가능했지만, 외부 모듈을 제공하면서 문서, 이미지, nib 과 같은 리소스를 제공할 필요성이 생겼다.

`framework` 는 이런 리소스를 소스화 함께 패키징해서 개발자들에게 제공하기 위한 용도로 만들어졌다

</br>

**한계**

`framework` 는 구조적 한계로 단일 플랫폼(iOS only, macOS only 등)만 지원 가능했으며,  
여러 종류의 아키텍쳐(x86 or arm32, arm64 등)를 지원하기 위해 `lipo` 명령을 통해 별도의 `fat binary` 로 아키텍쳐를 묶는 과정이 필요했다

</br>

또 배포전 앱 바이너리에 포함된 필요없는 시뮬레이터용 바이너리를 빼줘야 했었기 때문에 번거로웠다.

</br>

"하나의 모듈로 모든 플랫폼을 지원할 수 있으면서, 모든 아키텍쳐에도 대응할 수 있으면 편할 것 같은데?" 라는 발상에서 나온게 `xcframework` 라고 할 수 있다

</br>
</br>

### 계기

이 예제를 작성하게 된 계기는 실무에서 NaverMapSDK -> TMapSDK 로 전환한 뒤, 시뮬레이터가 동작하지 않게되었는데,  
운영환경에서 간헐적으로 발생하는 기기별, 운영체제별 원인을 발견하기위해서 반드시 시뮬레이터가 필요했다.

TMap 에서 제공하는 TMapSDk 는 `.xcframework` 가 아닌 `.framework` 로 제공했기에  
실기기 빌드를 하려면 시뮬레이터를 포기해야했고, 시뮬레이터를 빌드하려면 실기기를 포기해야했다.

하지만 개발하면서 실기기랑 시뮬레이터를 둘 다 포기할 수 없기때문에, .framework 를 .xcframework 로 묶어 사용할 수 있음을 확인했고
프로젝트에 .xcframework 를 적용해 동작시키는데 성공했다.

</br>

<!--TODO: xcframework 특성 작성 -->

</br>

## 프로젝트 생성

1. File > New > Project 선택
2. Framework 선택 및 프로젝트 생성

</br>

## 프로젝트 설정 및 소스 작성

1. 소스 작성 또는 작성된 소스 추가
2. 의존하는 외부 라이브러리가 있을 경우 Package Dependencies 에서 추가
3. Build Settings > Skip Install 'No' & Build Libraries for Distribution 'Yes' & Architectures 'arm64'(default) 변경
4. `Mach-o Type` 을 static 으로 설정(앱 실행 파일 내부에 포함하며, 별도의 사이닝이 필요없어짐)

</br>

## xcframework 빌드

.xcodeproj 가 있는 디렉토리에서 아래 명령 실행
루트 디렉토리에 `Build`, `Output` 디렉토리가 있는 상황을 가정

`xcframework` 를 만들기 전 중간 산출물을 만들기위헤 `.framework` 또는 `.xcarchive` 를 선택할 수 있는데,  

xcodebuild 의 `archive` 액션을 추가할 경우 결과물이 `.xcarchive` 로 나온다

아래 예시는 `.archive` 로 산출물을 엮어 `.xcframework` 로 만드는 예제다

</br>

```bash
xcodebuild archive -project <프로젝트파일> -scheme <스킴명> -sdk <SDK명> -destination <플랫폼명> -archivePath <아카이브내보낼경로>
```

</br>

### 1. 실기기용 .xcarchive 생성

```bash
xcodebuild archive -project NotificationServiceSupport.xcodeproj -scheme NotificationServiceSupport -sdk iphoneos -destination "generic/platform=iOS" -archivePath ./Build/NotificationServiceSupport.xcarchive
```

</br>

### (선택) 1-1. 시뮬레이터용 xcarchive 생성

시뮬레이터 동작이 필요없을경우 생략

```bash
xcodebuild archive -project NotificationServiceSupport.xcodeproj -scheme NotificationServiceSupport -sdk iphonesimulator -destination "generic/platform=iOS Simulator" -archivePath ./Build/NotificationServiceSupport_simulator.xcarchive
```

</br>

### 2. xcframework 생성 명령어 실행

`iOS 플랫폼 용` .xcarchive, `iOS Simulator용` .xcarchive 두 파일을 하나의 `.xcframework` 로 생성한다

```bash
xcodebuild -create-xcframework -archive ./Build/NotificationServiceSupport.xcarchive -framework NotificationServiceSupport.framework -archive ./Build/NotificationServiceSupport_simulator.xcarchive -framework NotificationServiceSupport.framework -output ./Output/NotificationServiceSupport.xcframework
```