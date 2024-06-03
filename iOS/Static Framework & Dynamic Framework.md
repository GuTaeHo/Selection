# Static Framework & Dynamic Framework

### 라이브러리 
앱(타겟) 에서 사용될 소스코드의 모음이다.  
이미지같은 리소스는 포함할 수 없으며 순수하게 소스코드로만 이루어져있다.

`라이브러리`는 앱(타겟)에 컴파일 또는 런타임 시점에 어떻게 링킹하는가에 따라 2가지 타입으로 나누어 볼 수 있다.  

**Static Library(`.a`)**  
컴파일된 타겟의 소스(object code)에 `static linker` 를 통해서 연결되며, `executable file` 에 복사된다.  
런타임 동안 계속 heap 영역에 유지된다. (메모리 점유)  
컴파일 시간이 오래걸린다. (실행파일에 항상 포함)

**Dynamic(Shared) Library(`.dylib`)**   
`linker` 는 라이브러리의 참조만을 저장하고 `excutable file` 에 라이브러리를 포함시키지않는다.  
런타임에서 필요한 시점에 동적으로 로드되어서 사용된다.
(매번 컴파일 할 필요 X)  
런타임 시 코드에 접근할 때 비교적 느리다.  
iOS 및 macOS 시스템 라이브러리(UIKit, Foundation 등)는 모두 **Dynamic Library** 이다.  
iOS 앱은 샌드박스로 구성되어있기 때문에 Dynamic Library 는 앱 번들에 포함되어있다.

<br>

### 프레임워크
헤더파일, 지역화(localizable) 파일, 이미지(asset) 문서와 같은 리소스를 하나로 묶어(`Bundle`) 제공하는 개념이다.  
프레임워크에 라이브러리가 포함될 수 있다.

### Static Framework

### Dynamic Framework


<br>

### 참고
iOS Library, Framework, Swift Package   
https://medium.com/delightroom/ios-library-framework-swift-package-%EC%95%8C%EC%95%84%EB%B3%B4%EC%9E%90-%EC%B0%A8%EC%9D%B4%EC%A0%90-1f42c7848771

Library vs Framework (static library, dynamic library, static framework, dynamic framework)  
https://ios-development.tistory.com/1004

Dynamic Library  
https://hcn1519.github.io/articles/2019-09/dynamic_library

iOS System Framework  
https://theapplewiki.com/wiki/Filesystem:/System/Library/Frameworks