# Flutter Command

일반적인 상황에서 `fvm` 을 설치 후 특정 버전의 flutter sdk 만 내려받으면 dart 버전이 함께 내려 받아짐  
(flutter 는 dart 버전에 종속적)

pubspec.yaml 파일을 연 뒤 `Pub get` 을 클릭해 의존성만 내려받으면 웹에서 실행이 가능함

</br>

## flutter SDK 설치 (단일 버전)

> !!! 플러터 버전 관리 도구인 `fvm` 을 사용할 경우 **flutter 설치** 부분을 건너뛸 것

```sh
brew install --cask flutter
```

</br>

## flutter version management (fvm) 활성화

### A. flutter 를 설치하지 않고 fvm 을 바로 설치할 경우

```sh
# 레포지토리 추가
brew tap leoafarias/fvm
# fvm 설치
brew install fvm
```

### B. flutter 를 설치한 경우

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

## flutter SDK 설치

```sh
fvm install <version>
# fvm install 3.10.6

# 최신 안정 버전 설치
fvm install stable
```

</br>

## 설치된 버전 확인

```sh
fvm list
```

</br>

## 프로젝트에 버전 적용

### A. 로컬(프로젝트) 수준 버전 사용 [권장!]

> 프로젝트 폴더 내 에서 명령어 실행

```sh
# 특정 버전 사용
fvm use <version>
# fvm use 3.10.6

# 최신 안정 버전 사용
fvm use stable
```

</br>

### B. 글로벌(전역) 수준 버전 사용

```sh
fvm global <version>
# fvm global 3.10.6

fvm global stable
```

</br>

### 로컬에 설치된 SDK 안드로이드 스튜디오에 적용하는 방법

1. `Settings` -> `Languages & Frameworks` -> `Flutter SDK Path`
2. 폴더 열기 누르고, 숨김 파일 표시 및 .fvm 폴더 선택
3. .fvm -> flutter_sdk 선택 (flutter 버전 설정하면 **dart 는 알아서 잡힘**)
4. `apply` 누르기
5. 현재 프로젝트를 터미널로 열고 아래 명령어로 의존성 내려받기 or `pubspec.yml` 열고 **pub get** 누르기

```sh
fvm flutter pub get
```

6. 실행

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

</br>

### 의존성 재설치

프로젝트 루트로 이동 후

```sh
flutter clean
flutter pub get
```

```sh
flutter doctor
```

</br>

### + JsonSerializable (데이터 직렬화)

`JSON` 형식의 데이터를 특정 `Model` 과 매핑이 필요할 경우가 있다.

`수동` 및 `자동 직렬화` 방식을 지원하지만, 오타 및 작성시간이 많이 걸리기 때문에  
`@JsonSerializable` 어노테이션을 이용한 `자동 직렬화`를 많이 사용한다

#### 수동 직렬화

```dart
class SystemInfo {
  final String cpuManufacturer;
  final String cpuBrand;
  final String cpuModel;
  final double memoryTotalMB;
  final double memoryUsedMB;
  final double memoryFreeMB;
  final double memoryUsagePercent;
  final double diskSize;
  final List<DiskInfo> diskInfos;

  SystemInfo({
    required this.cpuManufacturer,
    required this.cpuBrand,
    required this.cpuModel,
    required this.memoryTotalMB,
    required this.memoryUsedMB,
    required this.memoryFreeMB,
    required this.memoryUsagePercent,
    required this.diskSize,
    required this.diskInfos,
  });

  factory SystemInfo.fromJson(Map<String, dynamic> json) {
    return SystemInfo(
        cpuManufacturer: json['cpuManufacturer'] ?? '',
        cpuBrand: json['cpuBrand'] ?? '',
        cpuModel: json['cpuModel'] ?? '',
        memoryTotalMB: json['memoryTotalMB'] ?? 0,
        memoryUsedMB: json['memoryUsedMB'] ?? 0,
        memoryFreeMB: json['memoryFreeMB'] ?? 0,
        memoryUsagePercent: json['memoryUsagePercent'] ?? 0,
        diskSize: json['diskSize'] ?? 0,
        diskInfos: List<DiskInfo>.from((json['diskInfos'] ?? []).map((e) => DiskInfo.fromJson(e)),
      ),
    );
  }
}

class DiskInfo {
  final String mount;
  final double totalMB;
  final double usedMB;
  final double availableMB;
  final double usePercent;

  DiskInfo({
    required this.mount,
    required this.totalMB,
    required this.usedMB,
    required this.availableMB,
    required this.usePercent,
  });

  factory DiskInfo.fromJson(Map<String, dynamic> json) {
      return DiskInfo(
        mount: json['mount'] ?? '',
        totalMB: json['totalMB'] ?? 0,
        usedMB: json['usedMB'] ?? 0,
        availableMB: json['availableMB'] ?? 0,
        usePercent: json['usePercent'] ?? 0,
    );
  }
}
```

</br>

#### @JsonSerializable

1. `pubspec.yml` 에 패키지 추가

```yaml
# pubspec.yaml

dependencies:
  json_annotation: ^4.0.0

dev_dependencies:
  json_serializable: ^6.0.0
  build_runner: ^2.0.0
```

2. `pug get` 으로 의존성 추가

3. `Model` 작성

```dart
import 'package:json_annotation/json_annotation.dart';

// 파일명.g.dart 추가
part 'pi_system_info.g.dart';

@JsonSerializable()
class SystemInfo {
  final String cpuManufacturer;
  final String cpuBrand;
  final String cpuModel;
  final double memoryTotalMB;
  final double memoryUsedMB;
  final double memoryFreeMB;
  final double memoryUsagePercent;
  final double diskSize;
  final List<DiskInfo> diskInfos;

  SystemInfo({
    required this.cpuManufacturer,
    required this.cpuBrand,
    required this.cpuModel,
    required this.memoryTotalMB,
    required this.memoryUsedMB,
    required this.memoryFreeMB,
    required this.memoryUsagePercent,
    required this.diskSize,
    required this.diskInfos,
  });

  factory SystemInfo.fromJson(Map<String, dynamic> json) =>
      _$SystemInfoFromJson(json);
  Map<String, dynamic> toJson() => _$SystemInfoToJson(this);
}

@JsonSerializable()
class DiskInfo {
  final String mount;
  final double totalMB;
  final double usedMB;
  final double availableMB;
  final double usePercent;

  DiskInfo({
    required this.mount,
    required this.totalMB,
    required this.usedMB,
    required this.availableMB,
    required this.usePercent,
  });

  factory DiskInfo.fromJson(Map<String, dynamic> json) =>
      _$DiskInfoFromJson(json);
  Map<String, dynamic> toJson() => _$DiskInfoToJson(this);
}
```

4. 터미널에서 `build runner` 실행

```sh
flutter pub run build_runner **build**
```
