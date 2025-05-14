

## 개요
API 서버에 접근하여 데이터를 받아오기 위해서 일반적으로 토큰 인증 방식을 사용한다.
보안을 위해 토큰은 일정 시간뒤면 만료가 되는데, 문제는 만료될 때 마다 사용자에게 다시 로그인 시도를 요청해야한다는 점이다.
로그인(토큰 재발행)을 자동화하기 위해서 API 요청 후 HTTP 상태코드를 받아 저장된 정보로 재로그인을 시도하는 방법도 있겠지만, `Alamofire` 에서 제공하는 `AuthenticationInterceptor` 를 사용하는 방법을 알아보자.


> **AuthenticationCredential & Authenticator** 
프로토콜을 채택한 두 클래스를 생성자로 받아 헤더 토큰 추가, 만료 검사, 재 발행 등 수행.

DataRequest 의 intercepter 인자에 추가하여 기능 실행가능.

## AuthenticationCredential
> 액세스 토큰, 리프레시 토큰, 만료일, 재발행 필요 여부 등을 저장

```swift
import Alamofire 

struct MyAuthenticationCredential: AuthenticationCredential { 
	let accessToken: String 
	let refreshToken: String 
	let expiredAt: Date 

	// 입맛에 맞게 토큰 만료시간을 계산
	var requiresRefresh: Bool { Date(timeIntervalSinceNow: 60 * 5) > expiredAt } 
}
```

## Authenticator

> 토큰 추가, 토큰 만료 체크, 필수 인증 Request 체크, 토큰 재 발행 요청 등을 수행

**apply() - 요청을 가로챈 뒤, HTTP 헤더에 토큰 추가**
```swift
func apply(_ credential: Credential, to urlRequest: inout URLRequest) { 
	urlRequest.headers.add(.authorization(bearerToken: credential.accessToken)) 
	urlRequest.addValue(credential.refreshToken, forHTTPHeaderField: "refresh-token") 
}
```

**didRequest() - 응답 결과 필터링 (401 에러 체크)**
```swift
func didRequest(_ urlRequest: URLRequest, with response: HTTPURLResponse, failDueToAuthenticationError error: Error) -> Bool {
    return response.statusCode == 401
}
```

**isRequest() - Token 이 필요한 요청일 경우에만 재 발행**
```swift
func isRequest(_ urlRequest: URLRequest, authenticatedWith credential: Credential) -> Bool {
    let bearerToken = HTTPHeader.authorization(bearerToken: credential.accessToken).value
    return urlRequest.headers["Authorization"] == bearerToken
}
```

**refresh() - Token 재 발행 요청**
```swift
func refresh(_ credential: Credential, for session: Session, completion: @escaping (Result<Credential, Error>) -> Void) {
    // TODO: refresh API 콜
    /*
     switch result {
     case .success(let response):
        completion(.success(credential))
     case .failure(let error):
        completion(.failure(error))
     }
     */
}
```
## 참고
https://ios-development.tistory.com/732