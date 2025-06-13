# 딥 링크, URL 스킴, 유니버설 링크

URL 링크를 통해 앱을 실행하거나, 미 설치된 경우 스토어로 보내는 기능이 필요한 경우가 있다.

이러한 기능을 `Deep Link` 라고 부르며, `Deep Link` 의 구현 방식은 크게 2가지가 있다.

구현이 가장 간단한 `URL Scheme` (Custom Scheme) 방식 부터 알아보자.

</br>

### URL 스킴 방식

```
URL 스킴이 앱에 등록되어있다는 가정하에 진행

실 제품의 URL 스킴 대신 임의로 작성
```

</br>

**동작방식**

1. 사용자가 링크를 누르거나 검색
2. 웹 브라우저가 수신된 html, javascript 실행
3. 설치 유무 검사 및 앱 오픈 or 앱 스토어 링크로 리다이렉트

</br>

서버에 업로드 된 웹 사이트의 javascript 코드 동작 방식은 아래와 같다  


1. href 명령을 통해 앱의 URL 스킴(ex: "myapp://myapp")을 직접 호출

    ```js
    document.href = "myapp://myapp"
    ```

2. 브라우저는 할당된 URL 스킴과 일치하는 앱이 설치되어있다면 **실행한다**  
없다면 (브라우저마다 다르지만) 얼럿을 표시하거나 아무런 동작을 하지 않는다

    ```
    앱 설치 유무를 제공하는 API 가 없기 떄문에 앱이 열리지 않았을 경우 스토어 링크를 연다
    ```

3. 1초 뒤, 스토어 링크를 연다
    ```js
    setTimeout(() => {
        openStore()
    }, "1000");
    ```

</br>

스킴 방식은 구현이 간단하지만, 앱이 설치되어있지 않았을 경우,  
스토어로 보내지않고 **"잘못된 URL"** 이라는 얼럿만 띄우고 **동작하지 않는 경우**가 발생할 수 있다는 치명적인 단점이 있다.

</br>
</br>

## 유니버설 링크

Apple 에서 출시한 `iOS 전용 딥링크`이며 `macOS`, `watchOS`에서도 사용함

iOS 9 이상 부터 지원하기 시작했고, 기존 `URL Scheme` 방식의 단점을 보완한 방식

</br>

**동작방식**

1. 사용자가 링크를 누른다

    링크를 브라우저에 **직접 타이핑**할 경우, 즉시 이동하지않는다!
    메시지나 카메라 앱 (QR 코드) 로 링크를 클릭해야 정상동작한다

    </br>

    카카오 인앱 브라우저 동작 X

    </br>

2. `iOS` 가 해당 URL 과 연결된 앱이 설치되었는지 확인
3. 설치되어있다면 앱 실행, 없다면 해당 링크의 웹 사이트 오픈


</br>



### 구축방법

```
신뢰할 수 있는 루트 인증기관(`CA`)에서 발급한 인증서가 설치된  
`https` 프로토콜로 접근 가능한 웹 서버가 있다는 가정하에 진행

iOS 13 이상 버전을 가정하고 진행, iOS 12 이하는 `AASA` 포맷이 약간 다름
```

**주의사항**

- Content-Type이 application/json으로 전달되어야 한다.
- AASA 파일은 확장자가 없다. apple-app-site-association.json으로 표기하지 않도록 주의.
- `.well-known` 디렉토리 아래에 다른 디렉토리를 만들어 AASA 파일을 위치 시키지 말 것 (OS 가 인식하지 못함)
- iOS 는 AASA 파일을 다운로드 한 후 캐싱처리하기 때문에, AASA 파일을 수정한 뒤, 반영되지 않을 수 도 있다  
앱을 삭제하거나,  

</br>

1. apple-app-site-association (AASA) 파일 생성

    ```bash
    {
        "applinks": {
            "details": [
                {
                    "appIDs": ["TEAM_ID.BUNDLE_ID"],
                    "components": [
                        {
                            "/": "/test/*",
                            "?": {
                                "user": "gutaeho"
                            },
                            "comment": "for universal link test"
                        },
                        {
                            "/": "*"
                        }
                    ]
                }
            ]
        }
    }
    ```

    </br>

    `components`: 링크 접속 시 URL 매칭 유무를 검사해 맞는 URL 만 통과시킨다.
    `/`: URL path (`*` 는 전부를 의미) 일치유무 검사
    `?`: `/` 의 뒤에 쿼리 스트링 일치유무 검사
    `comment`: url 에 대한 메모

    </br>

    위의 컴포넌트를 해석하면 다음과 같다

    1. `https://domain.com/test` 아래 경로에 있는 모든 파일이고, 쿼리스트링이 `user=gutaeho` 일 때 통과한다.
    2. `https://domain.com` 아래 경로에 있는 모든 파일을 통과 시킨다.
    </br>

    **주의사항**

    ```
    components 는 위에서 차례대로 매칭을 시도한다.

    범용적인 패턴은 아래에 두는것을 권장
    ```

    </br>

2. 웹 서버 루트 디렉토리에 있는 .well-known 디렉토리 아래에 `AASA` 파일 위치

    ```
    없다면 "/.well-known" 디렉토리 생성한 뒤 넣으면 됨
    ```

3. 웹 서버 `AASA` MIME 타입 변경
    

4. Xcode > Target > Signing & Capabilities 탭 > + Capability -> Associated Domains 추가

    </br>

    **Domains 에 아래 내용 작성**

    ```
    applinks:example.com
    ```

5. 앱 빌드 및 추가된 도메인으로 접속

    ```
    브라우저에서 example.com 검색 후 앱 설치되어있다면 켜지고, 미 설치라면 index 페이지로 이동하는지 확인
    ```
