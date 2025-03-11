# xcframework 생성방법

## 프로젝트 생성
- File > New > Project 선택
- Framework 선택 및 프로젝트 생성

<br>

## 프로젝트 설정 및 소스 작성
- 소스 작성 또는 작성된 소스 추가
- 의존하는 외부 라이브러리가 있을 경우 Package Dependencies 에서 추가
- Build Settings > Skip Install 'No' & Build Libraries for Distribution 'Yes' & Architectures 'arm64'(default) 변경
- `Mach-o Type` 을 static 으로 설정(앱 실행 파일 내부에 포함하며, 별도의 사이닝이 필요없어짐)

<br>

## xcframework 빌드
> .xcodeproj 가 있는 디렉토리에서 아래 명령 실행
> 루트 디렉토리에 `Build`, `Output` 디렉토리가 있는 상황을 가정

<br>

### 1. xcarchive 생성 명령어 실행, xcodebuild 명령으로 .o 파일 생성
```bash
xcodebuild archive -project NotificationServiceSupport.xcodeproj -scheme NotificationServiceSupport -sdk iphoneos -destination "generic/platform=iOS" -archivePath ./Build/NotificationServiceSupport.xcarchive
```

<br>

### 2. 시뮬레이터에서도 동작하도록 시뮬레이터용 xcarchive 생성 명령어 실행
> 시뮬레이터 동작이 필요없을경우 생략

```bash
xcodebuild archive -project NotificationServiceSupport.xcodeproj -scheme NotificationServiceSupport -sdk iphonesimulator -destination "generic/platform=iOS Simulator" -archivePath ./Build/NotificationServiceSupport_simulator.xcarchive
```

<br>

### 3. xcframework 생성 명령어 실행
> `iOS 기기용` .xcarchive, `iOS Simulator용` .xcarchive 두 파일을 하나의 `.xcframework` 로 생성한다

```bash
xcodebuild -create-xcframework -archive ./Build/NotificationServiceSupport.xcarchive -framework NotificationServiceSupport.framework -archive ./Build/NotificationServiceSupport_simulator.xcarchive -framework NotificationServiceSupport.framework -output ./Output/NotificationServiceSupport.xcframework
```


