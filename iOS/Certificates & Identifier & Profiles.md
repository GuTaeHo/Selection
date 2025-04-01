## Certificates (인증서)
배포용 또는 개발용 앱을 서명하기 위해 필요한 인증서를 관리하는 섹션

iOS 앱을 개발하고 앱스토어 배포하고 싶다면,  
`Development` 인증서 와  
`Distribution` 인증서가 각각 필요하다.
  
`Development` 인증서는 개발용 앱을 빌드 및 설치할 때 사용하며,  
`Distribution` 인증서는 앱을 App Store 에 제출할 때 사용된다.


`Distribution` 인증서의 경우, 팀 간에 하나의 인증서만 사용하는게 일반적이다.

<br>

## Identifiers (식별자) 
애플의 시스템이 앱을 구분하는 식별자를 관리하는 섹션  

종류
- App IDs  
`1ABCDE2F34` 형태를 가진 앱의 고유한 아이디  
**Team ID** 라고도 불리며, 단일 팀의 앱들은 App ID 를 공유한다.

- Bundle ID  
`com.example.myApp` 형태를 가진 앱의 고유한 문자열,  
애플 시스템은 앱을 Bundle ID 를 통해 구분한다.

- Capability  
앱이 어떤 부가적인 기능을 가지는지 설정할 수 있다  
(Push Notifications, In-App Purchase 등)


**Capability 주의사항**  
`Identifiers` 에서 설정되는 Capability 는 부가적인 기능을 추가하기 위한 전단계이며,  
**Identifiers 가 Bundle ID와 매칭되어 등록이 되어있어야 Xcode 상에서 Capability 를 추가해 실제 기능을 구현할 수 있음**

<br>

## Profiles (Provisioning Profile)
앱을 테스트하려는 기기에 설치되는 프로파일
이 프로파일과 일치하는 앱만이 실행될 수 있음

프로파일에 담긴 정보는 다음과 같음
- Type  
	`개발용(Development)` 과 `배포용(Distribution)` 두 가지로 나뉨  
	개발용일 경우 어떤 디바이스에 동작하는지 지정되어있음  
	(ex: iOS[+iPadOS, watchOS, visionOS], tvOS, macOS) 

	배포용일 경우 어떤 방식으로 배포될 지 지정되어있음  
	(ex: Ad Hoc, App Store Connect, Mac App Store Connect..)
- App ID (Bundle ID)  
	App ID 와 일치하는 앱을 실행할 수 있음을 나타냄
- Device ID (UDID)  
	프로파일에 등록된 Device 만이 앱을 실행할 수 있음
- Certificates (배포 & 개발용 인증서)  
	해당 프로파일이 배포용 프로파일인지, 개발용 프로파일인지 나타냄
- Expiration Dates (만료일)  

<br>

