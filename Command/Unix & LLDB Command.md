# Unix & LLDB Command

`Unix`, `macOS`, `LLDB` 명령어 및 효과적이고 자주 사용되는 디버깅 방법 정리

</br>

## Mach-O

**Mach Object file format** 의 준말로 **Mach** 커널을 사용하는 운영체제(macOS, iOS ...)의 실행파일 포맷이다.

특정 chunk 로 그룹화된 일련의 바이너리 형태로 구성되어있으며

확장자로 `.app`, `.bundle`, `.o`, `.dylib`, `.a` 등이 있다

</br>

**포맷**

- `.app`: macOS 또는 iOS 실행파일이 포함된 패키지(디렉토리)
- `.bundle`: 리소스(소스가 포함될 수 도 있다)가 포함된 패키지(디렉토리), 런타임에 로드됨
- `.o`: 오브젝트 파일, 소스코드를 컴파일해 나온 기계어 형태의 결과물 
- `.dylib`: 동적 라이브러리, 런타임에 로드되는 라이브러리, `iOS` 는 서드 파티 `dylib`를 제한하기 때문에 주의
- `.a`: 정적 라이브러리, 컴파일 타임에 링크되는 라이브러리
- `.framework`: 프레임워크, 정적 or 동적으로 링킹됨

</br>
`.dylib`, `.bundle` 의 차이점이 없는 것 아니냐 라고 의문이 들 수 있다

</br>

`.dylib` 는 리소스를 포함할 수 없고, 여러 앱이 사용가능하며, os 가 로드한다  
`.bundle` 은 리소스를 포함할 수 있고, 단일 앱에 적용되며, 앱이 직접 로드한다는 차이점이 있다.

추가적으로 `framework` 는 리소스를 포함할 수 있고, 여러 앱이 사용가능하다.  
하지만 `bundle` 과는 다르게 사용시 import 로 프레임워크를 추가해줘야하며, 정적으로 로드될 수도 있다

</br>

Mach-O 포맷의 파일을 분석할 수 있는 도구로 `lipo`, `otool`, `vtool` 가 있는데,  
이 도구들은  macOS 의 **command line tools** 에 기본적으로 포함된 도구들이라  
command line tools 만 설치되어있다면 개별적인 패키지 설치과정이 필요없다

</br>

## file

이 명령어는 **command line tools** 이 아닌 리눅스에서 제공되는 명령어다.

파일의 정보를 출력하기 위해 사용한다

```bash
file MyFramework.framework/MyFramework

# 출력결과
# MyFramework.framework/framework: Mach-O 64-bit dynamically linked shared library x86_64

# 해석
# Mach-O 포맷의 파일, 64bit 용으로 만들어짐, 동적 링크됨, x86_64 아키텍쳐에서 동작함
```

</br>

## lipo

### 아키텍처 확인

Mach-O 기반 바이너리 (`.a`, `.dylib`, `.app` ...) 의 확장자를 가지고 있을 경우  
`lipo -info` 명령을 통해서 어떤 아키텍쳐를 타겟으로 만들어진 프레임워크인지 확인할 수 있다.

`lipo -info` 명령은 framework 가 `fat file` (다중 아키텍처 지원) 인지, `non-fat` (단일 아키텍처 지원) file 인지 확인할 수 있다.

</br>

```bash
lipo -info <파일명>
# lipo -info MyFramework.framework/MyFramework
# // Non-fat file: MyFramework.framework/MyFramework is architecture: x86_64
```

</br>

## otool

`lipo` 는 아키텍쳐 정보는 알 수 있지만, 어떤 플랫폼을 위해 만들어진 건지 알 수 없다.

하나의 예시로 framework 가 arm 기반의 실 기기만 지원하는지,  
시뮬레이터로도 빌드 될 수 있는지 확인하기 위해 `otool` 명령을 사용할 수 있다.

</br>

### 헤더 정보 확인

앱에 포함된 프레임워크 정보를 확인 해 볼 경우, 아래 순서대로 진행

1. Xcode > Product > Show Build Folder in Finder > Products > (실기기, 개발용 ios 앱 일 경우) Debug-iphoneos 로 이동
2. `.framework`, `.o`, `.app`, `.bundle` 등 수 많은 파일 표시 됨
3. `.app` 파일 우 클릭 후 패키지 내용 보기
4. `Frameworks` 디렉토리 열기
5. 해당 폴더를 터미널로 열고 아래 명령어 입력

</br>

```bash
otool -hv MyFramework.framework/MyFramework
# otool -hv FirebaseAnalytics.framework/FirebaseAnalytics

# 출력결과
# FirebaseAnalytics.framework/FirebaseAnalytics (architecture arm64):
#Mach header
#      magic  cputype cpusubtype  caps    filetype ncmds #sizeofcmds      flags
#MH_MAGIC_64    ARM64        ALL  0x00       DYLIB    #15        688   NOUNDEFS DYLDLINK TWOLEVEL #NO_REEXPORTED_DYLIBS
```

- magic (바이너리 형식): `0xfeedfacf` == `MH_MAGIC_64` (64bit, little-endian, mach-o 형식의 실행 파일 or 라이브러리)
- cputype (지원 cpu 아키텍처): arm64
- filetype (Mach-O type): 동적 라이브러리

</br>

### 플랫폼 정보 확인

```bash
otool -l MyFramework.framework/MyFramework | grep -A 5 LC_BUILD_VERSION
# otool -l FirebaseAnalytics.framework/FirebaseAnalytics | grep -A 5 LC_BUILD_VERSION

# 출력결과
#      cmd LC_BUILD_VERSION
#  cmdsize 32
# platform 2
#    minos 13.0
#      sdk 18.5
#   ntools 1

```

- platform: 2 (iOS)
- minos: iOS 13 이상에서 사용가능
- sdk: 빌드 시 사용된 SDK 버전

</br>

### .app 에 연결된 dynamic framework 확인

```bash
# 명칭이 MyApp.app 일 경우
otool -l MyApp.app/MyApp

# 출력결과
# MyApp.app/MyApp:
# /System/Library/Frameworks/CoreGraphics.framework/CoreGraphics (compatibility version 64.0.0, current version 1889.5.3)
# /System/Library/Frameworks/CoreText.framework/CoreText (compatibility version 1.0.0, current version 844.5.0)
# /System/Library/Frameworks/Foundation.framework/Foundation (compatibility version 300.0.0, current version 3502.0.0)
# ...
# @rpath/NaverThirdPartyLogin.framework/NaverThirdPartyLogin (compatibility version 1.0.0, current version 1.0.0)
# @rpath/NMapsMap.framework/NMapsMap (compatibility version 1.0.0, current version 1.0.0)
# @rpath/NMapsGeometry.framework/NMapsGeometry (compatibility version 1.0.0, current version 1.0.0)
```

위 결과의 `/System` 경로에 있는 프레임워크는 `System Dynamic Framework` 이며, 모든 앱이 하나의 프레임워크를 공유한다

`@rpath` 경로에 있는 프레임워크는 해당 앱에만 링크된 `Dynamic Framework` 이며, 해당 앱 번들 내부에서만 참조가능하다\

**자세한 내용**은 [Static Framework & Dynamic Framework](../iOS/Static%20Framework%20&%20Dynamic%20Framework.md) 참고

</br>
</br>

## nm

`otool` 은 동적 링크된 `Dynamic Framework` 목록은 확인할 수 있으나, 앱 바이너리에 링크된 `Static Framework` 는 확인이 불가능하다

`nm` 명령은 실행 바이너리에 병합된 Framework 와 해당 프레임워크가 사용하는 목적 파일(`.o`) 심지어 어떤 심볼(함수, 변수)가 있는지도 출력이 가능하다

</br>

```bash
# 디버그 심볼이 포함된 MyApp 의 목적 파일만 출력
nm -debug-syms MyApp | grep "\.o"
```

</br>

### 주의사항

`nm` 명령을 수행해도 아무런 결과가 표시되지않는 경우가 있다

이는 앱을 빌드할 때 실행바이너리의 **디버그 심볼 정보를 모두 제거하는 옵션**이 켜져 있을 경우 발생한다.

단, 실제 배포용 앱에서는 보안상 취약할 뿐 아니라 심볼정보로 인한 앱 사이즈가 커지기 떄문에

**테스트 환경에서만 비활성화하는것을 권장한다**

</br>

#### 비활성화 방법

1. Xcode > Tartgets > Build Settings 열기
2. `Strip Debug Symbols During Copy`, `Strip Linked Product`, `Strip Style` 설정

`Strip Debug Symbols During Copy` 은 `No`,  
`Strip Linked Product` 은 `No`,  
`Strip Style` 은 `Debugging Symbols`

로 설정 시 실행파일 내부에 심볼정보가 남게된다

</br>
</br>

## vtool

`otool` 의 복잡한 플랫폼 정보 결과물을 직관적으로 출력하는 도구

</br>

**실기기.app 바이너리 출력**
```bash
vtool -show-build MyFramework.framework/MyFramework
# vtool -show-build KakaoSDKCertCore.framework/KakaoSDKCertCore

# 출력결과
# KakaoSDKCertCore.framework/KakaoSDKCertCore:
# Load command 7
#       cmd LC_BUILD_VERSION
#   cmdsize 32
#  platform IOS
#     minos 13.0
#       sdk 18.5
#    ntools 1
#      tool LD
#   version 1167.5
```

</br>

**시뮬레이터.app 바이너리 출력**
```bash
vtool -show-build MyFramework.framework/MyFramework
# vtool -show-build KakaoSDKCertCore.framework/KakaoSDKCertCore

# 출력결과
# KakaoSDKCertCore.framework/KakaoSDKCertCore:
# Load command 7
#       cmd LC_BUILD_VERSION
#   cmdsize 32
#  platform IOSSIMULATOR
#     minos 13.0
#       sdk 18.5
#    ntools 1
#      tool LD
#   version 1167.5
```

</br>
</br>

## po (Print Object)

객체의 형태(설명)을 출력하는 명령어

</br>

### 레이아웃 충돌 시 주소로 어떤 뷰인지 찾는 방법

충돌 발생직후, View Hierarchy 를 실행한 뒤,
`po` 명령으로 주소에 해당하는 뷰의 특성을 알 수 있다.

```lldb
# ex) 충돌난 뷰의 주소가 "0x11bf90f00" 일 때
po 0x11bf90f00

# ex) 충돌난 뷰의 부모 뷰 획득
po [0x11bf90f00 superview]

# ex) 충돌날 뷰의 전체 뷰 트리 출력
po [0x11bf90f00 recursiveDescription]
```


</br>
</br>

## frame

현재 스택 프레임의 정보를 출력하는 명령어.

특정 함수의 이름, 파일명 및 라인 번호, 지역 변수 등을 출력할 수 있다.

</br>

### 스택 프레임 지역 & 멤버 변수 출력

```lldb
frame variable

# or 

fr v
```

### 스택 프레임 정보 출력

```lldb
frame info

# or

fr i
```

</br>
</br>

## bt (backtrace)

현재 스레드의 콜 스택을 출력하는 명령어.

</br>

### 현재 스레드의 콜 스택 출력하기

브레이크 포인트가 걸렸을 때, Xcode 상의 Debug Navigator 에서 백트레이스를 볼 수 있지만,  
클로저(특히 @escaping)의 경우 추적이 잘 안되는 경우가 있다.

이 때, `LLDB` 명령어로 현재 스레드의 백트레이스를 찍어볼 수 있다

```lldb
thread backtrace

# or

bt
```