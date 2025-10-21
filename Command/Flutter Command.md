# Flutter Command

> !!! 플러터 버전 관리 도구인 `fvm` 을 사용할 경우 **flutter 설치** 부분을 건너뛸 것

일반적인 상황에서 `fvm` 을 설치 후 특정 버전의 flutter sdk 만 내려받으면 dart 버전이 함께 내려 받아짐  
(flutter 는 dart 버전에 종속적)

**안드로이드 스튜디오를 사용할 경우**

`Settings` -> `Languages & Frameworks` -> 설치된 flutter SDK 경로

위 설정 완료 후

pubspec.yaml 파일을 연 뒤 `Pub get` 을 클릭해 의존성만 내려받으면 웹에서 실행이 가능함

</br>

## flutter 설치

```sh
brew install --cask flutter
```

</br>

## flutter version management (fvm) 활성화

### flutter 를 설치하지 않고 fvm 을 바로 설치할 경우

```sh
# 레포지토리 추가
brew tap leoafarias/fvm
# fvm 설치
brew install fvm
```

### flutter 를 설치한 경우

1. 루트 이동 및 fvm 활성화

```sh
flutter pub global activate fvm
```

2. .zshrc 에 환경변수 설정

```sh
export PATH="$PATH":"$HOME/flutter/bin"
export PATH="$PATH":"$HOME/bin/cache/dart-sdk/bin"
export PATH="$PATH":"$HOME/.pub-cache/bin"
```

3. 적용

```sh
source ~/.zshrc
```

</br>

## 사용가능 (릴리즈) 버전 확인

```sh
fvm releases
```

</br>

## 특정 버전 설치

```sh
fvm install <version>
# fvm install 3.10.6
```

</br>

## 설치된 버전 확인

```sh
fvm list
```

</br>

## 프로젝트에 버전 적용

```sh
# 로컬(프로젝트) 수준 버전 사용
fvm use <version>
fvm use 3.10.6

# 글로벌(전역) 수준 버전 사용
fvm global <version>
fvm global 3.10.6
```

</br>
</br>

## 의존성

### 의존성 추가

`pubspec.yaml` 파일 열기

```sh
dependencies:
    <module_name>: <version>
    # get: ^4.0.0
```

### 의존성 재설치

프로젝트 루트로 이동 후

```sh
flutter clean
flutter pub get
```

```sh
flutter doctor
```
