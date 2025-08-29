# multipart & multipart/form-data

## 클라이언트 -> 서버로 이미지를 업로드할 때

jpg, png 형태 그대로 전송되는 것이 아닌 **이미지도 문자**로 이루어져 있기 때문에,

문자를 HTTP request body에 담아서 서버로 전송한다.

</br>

## multipart/form-data

클라이언트에서 HTTP 요청 시 HTTP header 에 인코딩 타입을 지정할 수 있다.

서버는 이 **인코딩 타입을 보고** request body 가 어떤 타입의 내용인지 해석한다.

헤더에 인코딩 타입을 지정하기 위해서 사용하는 필드가 **Content-type** 이고, 여기에는 **MIME**(Multipurpose Internet Mail Extensions) 타입을 기술할 수 있는데, 여러 타입 중 하나가 **multipart** 임.

인코딩 타입은 request method 가 **post** 인 경우에만 사용할 수 있음 (request body 에 데이터를 담는건 post 만 가능하기 때문에 그런 것으로 보임)

</br>

## 인코딩 타입 (HTML 5 기준)

**application/x-www-form-urlencoded**: **기본값**, 서버에게 본문의 모든 문자가 인코딩 됨을 알림

> 여기서 왜 굳이 인코딩을 하느냐...? -> 한글이나 특수문자같은 ASCII 에 해당되지않는 문자들은 3byte 를 하나의 문자
> (ex url 인코딩 된 body를 서버가 이진수로 읽으면 11101010 10110000 10000000 을 한 묶음으로 해석하여 이숫자에 해당하는 문자[한글 "가"]로 치환함) 
> 로 읽어야 원 뜻이 전달 되지만 인코딩을 하지않고 서버에서 1byte 씩 ("가" 라는 문자로 인식되지 않고 다른 3개의 문자로 인식) request body 를 해석한다면 전혀 다른 문자로 치환되어 서버에서 처리될 것임

**multipart/form-data**: 모든 문자를 인코딩하지 않음 명시 (이미지나 영상의 문자가 인코딩 되면 깨질 것이므로...)

**text/plain**: 공백문자(space)만  "+" 기호로, 나머지는 인코딩되지 않음 명시

</br>

## multipart

일반적으로 request body 에는 한 종류의 타입이 대부분이고, 따라서 Content-type 도 타입을 하나만 명시할 수 있음

하지만 이미지 파일의 경우 사진 그 **자체의 데이터**와 사진에 대한 부가 설명이 있는 **메타 데이터**가 함께 전송되어야함

사진 데이터는 인코딩이 되지 않아야하고, 부가 설명은 인코딩이 되어야하기 때문에,

이 두 종류의 데이터가 하나의 request body 에서 구분될 필요성이 있음

그래서 등장한 것이 **multipart** 타입임

</br>

**[참고]**

https://velog.io/@shin6403/HTTP-multipartform-data-%EB%9E%80

https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=j880825&logNo=221231640609

http://www.tcpschool.com/html-tag-attrs/form-enctype
