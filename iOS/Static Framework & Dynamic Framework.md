# Static Framework & Dynamic Framework

## 라이브러리

특정 기능을 수행하는 코드를 사용할 수 있도록 미리 구현해놓은 코드의 집합체.

필요한 기능만 선택적으로 사용할 수 있으며, 개발자가 라이브러리를 호출하여 기능을 실행할 수 있다.

검증된 라이브러리는 개발시간을 단축하고, 동작의 신뢰성을 보장받을 수 있다.

</br>
</br>

## 프레임워크

프로그램의 전체적인 흐름을 관리하며, 개발자는 프레임워크가 제공하는 규칙에 따라서 프로그램을 작성해야한다.

개발에 도움이되는 여러가지 도구를 기본적으로 가지고 있으며,  
라이브러리와 프레임워크의 가장 큰 차이점은 **누가 프로그램의 흐름을 제어하는가** 에 있다.

대표적인 프레임워크  

### 서비스 개발

- 웹 서비스 개발에 사용되는 `Spring`
- 데이터 베이스를 객체 형태로 쉽게 다룰 수 있도록 도와주는 `MyBatis` 와 `SPA`

### 사용자 인터페이스 개발

- 웹 환경의 GUI 를 개발하는데 사용되는 `Vue.js`
- 안드로이드, iOS, 윈도우 및 리눅스 용 GUI 를 개발하는데 사용되는 `Flutter`

</br>

## iOS 에서의 라이브러리

앱(타겟) 에서 사용될 소스코드의 모음이다.  
이미지같은 리소스는 포함할 수 없으며 순수하게 소스코드로만 이루어져있다.

`라이브러리`는 앱(타겟)에 컴파일 또는 런타임 시점에 어떻게 링킹하는가에 따라 2가지 타입으로 나누어 볼 수 있다.  

</br>

**Static Library(`.a`)**  

- 컴파일된 타겟의 소스(object code)에 `static linker` 를 통해서 연결되며, `executable file` 에 복사된다.  
- 런타임 동안 계속 heap 영역에 유지된다. (메모리 점유)  
- 컴파일 시간이 오래걸린다. (실행파일에 항상 포함)

</br>

**Dynamic(Shared) Library(`.dylib`)**

- `linker` 는 라이브러리의 참조만을 저장하고 `excutable file` 에 라이브러리를 포함시키지않는다.  
- 앱 실행 시 동적으로 로드되어서 사용된다.
- 컴파일 시간이 비교적 짧다. (매번 컴파일 할 필요 X, 증분 빌드)  
- iOS 및 macOS 시스템 라이브러리(UIKit, Foundation 등)는 모두 **Dynamic Library** 이다.  
- iOS 앱은 샌드박스로 구성되어있기 때문에 Dynamic Library 는 앱 번들에 포함되어있다.

</br>

## iOS 에서의 프레임워크

헤더파일, 지역화(localizable) 파일, 이미지(asset) 문서와 같은 리소스를 하나로 묶어(`Bundle`) 제공하는 개념이다.  
프레임워크에 라이브러리가 포함될 수 있다.

</br>

Xcode 상에서 정적 & 동적 프레임워크 관련 옵션을 살펴보자.

</br>

### Link Binary With Libraries (= 정적 or 동적 라이브러리)

> 경로: Xcode > Build Phases > Link Binary With Libraries

</br>

**타겟과 라이브러리(모듈) 을 링크 시킬지 여부를 나타냄**

- 정적 라이브러리의 경우 여기에 포함되면 컴파일 시점에 링커가 앱 바이너리에 라이브러리를 포함(링킹) 시킴

- 동적 라이브러리의 경우 여기에 포함되면 @rpath 테이블에 기입되지만, 실제 모듈은 아직 Frameworks 폴더 아래에 없기 때문에, 실행 시 **dyld library not loaded 에러가 발생**함

동적 라이브러리는 앱 실행 시 @rpath 테이블을 읽고 그 때 라이브러리를 일괄 로딩함

> @rpath 경로는 xcodeproj > Build settings > Linking > Runpath Search Paths 에서 확인 가능함

</br>

동적 라이브러리는 `Embed & Sign` 또는 `Do not Embed` 옵션이 있는데, `Do not Embed` 일 경우 위와 같은 에러가 발생함  
(자세한 설명은 아래 `왜 Do not Embed 옵션이 있을까?` 참고)

Xcode 상에서 `Embed & Sign` 으로 변경하면 `Embed Frameworks` 섹션에 embed 한 라이브러리가 생김

</br>
</br>

## Embed Frameworks

> 경로: Xcode > Build Phases > Embed Frameworks

</br>

**타겟에 embed 된 라이브러리(모듈) 을 나타냄**

정적 라이브러리는 기본적으로 앱 바이너리에 병합되기때문에 `embed` 된 동적 라이브러리만 표시됨

</br>

### 왜 Do not Embed 옵션이 있을까?

다른 타겟이지만 하나의 앱 번들에 포함된 `App Extension` 등 에서 외부 의존성을 사용하는 경우,  

> ex) 위젯에서 메인 앱에서 사용중인 Alamofire 를 같이 사용해야하는 경우

정적 링킹을 통해 사용 시 모듈이 복사되어 실행 바이너리가 생성된다. 즉 동일한 모듈이 2개가 됨 (사이즈 증가)

만약 동적 링킹을 통해 타겟에 경로만 추가하고, 메인 앱에 모듈을 `embed` 할 경우,
동일한 모듈을 참조하기 때문에 모듈의 코드를 공유하게 된다.

만약 Extension 이 많으며 동일한 의존성을 함께 사용할 경우, 앱 사이즈를 줄이는데 도움이 되기 때문에 사용함.

</br>

### 동적 링킹의 경우 런타임에 적재되는데, 메모리 상의 이점이 있을까?

**없음**. 동적링킹은 앱이 실행될 때 모든 동적 프레임워크 목록을 읽고 불러오기 때문

> 단, iOS 에서 제공하는 시스템 프레임워크 (ex: UIKit, Foundation) 의 경우, 모든 앱이 공유하기 떄문에 이점이 있음

동적 링킹의 장점은 하나의 번들에 포함된 의존성이 코드를 공유하여 저장공간을 최적화하는데 목적이 있음

</br>
</br>

### 동적 프레임워크 적용 시 주의 사항

1. 동적 프레임워크는 효율적인 빌드(최초 빌드된 모듈이 변경되지않은 경우, 빌드 속도 향상을 위해 이후 부터 생략)를 위해, 캐싱처리 됨  
(새로운 동적 프레임워크를 추가한경우 반드시 `DelivedData` 와 캐시를 날려주고 새로 빌드해야 정상 빌드됨)

2. 다른 모듈에서 동일한 라이브러리 를 static 혹은 dynamic 으로 의존할 경우, 시스템 내부적으로 심볼이 충돌해 오류가 발생한다.  
(ex: Alamofire Dynamic, Moya 를 한 타겟에서 사용하는 경우 Moya 가 Alamofire 를 의존하지만 Static 만 지원하기 때문에 심볼 중복)

3. 동적 프레임워크는 다른 모듈에서 특정 하나의 모듈(ex: SnapKit 등) 을 의존하지만, 서로 다른 버전일 경우 심볼이 충돌해 오류가 발생한다.  
단, 정적 프레임워크는 각각의 모듈이 의존성을 따로 복사하여 가지고 있기 때문에 문제 X

</br>

### 참고

iOS Library, Framework, Swift Package   
https://medium.com/delightroom/ios-library-framework-swift-package-%EC%95%8C%EC%95%84%EB%B3%B4%EC%9E%90-%EC%B0%A8%EC%9D%B4%EC%A0%90-1f42c7848771

Library vs Framework (static library, dynamic library, static framework, dynamic framework)  
https://ios-development.tistory.com/1004

Dynamic Library  
https://hcn1519.github.io/articles/2019-09/dynamic_library

iOS System Framework  
https://theapplewiki.com/wiki/Filesystem:/System/Library/Frameworks