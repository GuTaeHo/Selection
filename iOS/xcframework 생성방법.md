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

<br>

## xcframework 빌드
.xcodeproj 가 있는 디렉토리에서 아래 명령 실행
루트 디렉토리에 `Build`, `Output` 디렉토리가 있는 상황을 가정

<br>

### 1. xcarchive 생성 명령어 실행, xcodebuild 명령으로 .o 파일 생성
```bash
xcodebuild archive -project NotificationServiceSupport.xcodeproj -scheme NotificationServiceSupport -sdk iphoneos -destination "generic/platform=iOS" -archivePath ./Build/NotificationServiceSupport.xcarchive
```

<br>

### 2. 시뮬레이터에서도 동작하도록 시뮬레이터용 xcarchive 생성 명령어 실행

시뮬레이터 동작이 필요없을경우 생략

```bash
xcodebuild archive -project NotificationServiceSupport.xcodeproj -scheme NotificationServiceSupport -sdk iphonesimulator -destination "generic/platform=iOS Simulator" -archivePath ./Build/NotificationServiceSupport_simulator.xcarchive
```

<br>

### 3. xcframework 생성 명령어 실행

`iOS 기기용` .xcarchive, `iOS Simulator용` .xcarchive 두 파일을 하나의 `.xcframework` 로 생성한다

```bash
xcodebuild -create-xcframework -archive ./Build/NotificationServiceSupport.xcarchive -framework NotificationServiceSupport.framework -archive ./Build/NotificationServiceSupport_simulator.xcarchive -framework NotificationServiceSupport.framework -output ./Output/NotificationServiceSupport.xcframework
```