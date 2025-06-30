## Unicode & Base64 & Data Class

### Unicode?

> 전 세계에서 사용되는 문자를 컴퓨터에서 일관되게 표시하고 다루기 위한 국제표준이다.
> 기존에는 ASCII, ISO-8859-1 등 나라마다 특화된 인코딩 방식을 사용하여, 각국의 언어에만 제한적으로
> 동작하였으며, 다른 문자를 표현하기 위해 별도의 인코딩 방식을 사용해야했었다.
>
> - ex) "글자가 깨진다" 라는 현상의 원인이 여기에 있다.
>
> 유니코드는 최소 1byte ~ 최대 4byte 를 사용해 한 문자를 표현하며, 한글은 3byte 를 사용해 한문자를 표현한다.
>
> 가장 널리 사용되는 인코딩 방식인 UTF-8 은 **가변 길이 인코딩 방식**으로 최소 1byte ~ 최대 4byte 를 사용해 문자를 표현한다.

  

### Base64

> 직역하면 "64진법" 이라는 뜻이며, 사진이나 영상같은 이진데이터를 텍스트로 변환한뒤 전송 & 저장하기 위해 사용되는 인코딩 방식이다.

**Base64 인코딩 방식**

1. 문자열을 ASCII 로 치환 후 바이너리로 변환한다
   - ex) a -> 97, 97 -> 01100001
2. binary data를 6비트씩 분리한다
   - ex) abc -> 01100001 01100010 01100011, 011000 010110 001001 100011
3. base64 색인표에서 분리된 문자를 치환한다 
   - ex) 011000 010110 001001 100011 -> YWJj
4. 만약, 6비트씩 분리하고 남는 공간이 있다면 0으로 채운다

**왜 사용할까?**

> Base64 로 인코딩하는 경우 데이터 사이즈가 약 33% 정도 커지며, 별도의 인코딩 & 디코딩 처리를 위한 추가연산까지 필요하다
> 그럼에도 사용하는 이유는 다음과 같다.
>
> 1. 시스템 별로 ASCII 를 처리하는 방식이 다 다르다.
>    - Base64는 ASCII 의 문자 집합만을 사용하므로 안전하다.
> 2. 일부 제어문자 (줄바꿈, 역슬래시 등...)의 경우 시스템 별로 다른 코드값을 갖는다.
>    - 줄바꿈 예시) Window (\r\n), Mac (\r), Unix & Linux (\n)
> 3. 일부 프로토콜에서는 텍스트만 전송이 가능하다
>    - 대표적인 예시로 HTTP. HTTP는 텍스트로 이루어진 데이터만 전송이 가능하다.

  

### Data Class

> swift 의 Data 란 바이트 버퍼(데이터를 전송 or 저장하기 위한 임시 공간)를 표현하는 데이터 타입이며, 
> 파일로부터 데이터를 읽거나, 네트워크를 통해 전송된 데이터를 처리하거나, 이진 데이터를 다루고 처리하는데 사용된다.

**Data 생성 및 인코딩 & 출력 예시**

```swift
import Foundation

// 바이트 배열로 데이터 생성
let byteArray: [UInt8] = [0x48, 0x65, 0x6C, 0x6C, 0x6F] // "Hello"의 ASCII 코드
let data = Data(byteArray)

// 데이터 길이 출력
print("Data length: \(data.count)")

// 데이터를 문자열로 변환
if let string = String(data: data, encoding: .utf8) {
    print("Data as string: \(string)") // "Hello"
}
```



  

**참조**

- https://effectivesquid.tistory.com/entry/Base64-%EC%9D%B8%EC%BD%94%EB%94%A9%EC%9D%B4%EB%9E%80 (Base 64)
- https://devuna.tistory.com/41 (Base 64)
- https://ko.wikipedia.org/wiki/%EB%B2%A0%EC%9D%B4%EC%8A%A464#cite_ref-2 (Base 64)
- https://codedragon.tistory.com/5103 (텍스트 파일 & 이진 파일)
- Chat-GPT