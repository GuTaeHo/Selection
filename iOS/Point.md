# Point

## 개요
iOS 앱을 개발하다보면 `Assets.xcassets` 에 접근하여 색상이나, 이미지, 영상등의 리소스를 가져와 사용하게되는데, 여기서 Image Set 을 생성하게되면 기본적으로 동일한 이미지를 3가지 방식(1x, 2x, 3x)으로 저장한다.

왜 동일한 사진을 3개씩이나 저장하는지 알아보도록하자.

</br>
</br>

**Pixel (= px)**
디스플레이를 구성하는 가장 작은 단위.

ex) `iPhone 15 pro` 가로: 2556px, 세로: 1179px

</br>

### Point (= pt)
다양한 해상도를 가진 기기가 등장하면서 여러 해상도를 하나의 단위로 지원하기 위한 최소단위.

Swift 에서는 대부분의 사이즈를 논리적인 단위인 `Point` 로 처리한다.

</br>

아이폰 오리지널 (1 ~ 3gs까지) 에서는 1pixel 에 1point 로 대응을 해도 문제가 없었지만,
아이폰 4가 등장하고나서 급격하게 해상도가 증가(Retina Display) 해버리자,
픽셀로 작업된 결과물이 너무 작아져 버리는 문제가 발생했다.

이러한 문제를 방지하고자, 작업 시 사용되는 모든 단위를 `Point` 로 지정하고
실제기기에 렌더링할 때 **일정 배율(아래 `Scale Factor` 참고)**을 곱해서 다양한 해상도를 지원할 수 있게되었다.

실제 코드상에서 `UIScreen 의 사이즈(iPhone 15 pro)`를 찍어보면 다음과 같이 표시된다
```swift
// 실제 기기의 물리적인 사이즈 
print(UIScreen.main.nativeBounds.size) // (1179.0, 2556.0)
// 논리적인 사이즈 (= Point)
print(UIScreen.main.bounds.size) // (393.0, 852.0)
```

</br>
</br>

### Scale Factor
> 위키백과에서 정의한 스케일 팩터(`Scale factor`)는 어떤 양을 늘리거나 줄이거나 또는 곱하는 수이다.
> iOS 에서는 Point 를 Pixel 로 변환할 때 적용되는 배율을 의미한다.

`Scale Factor` 의 예시를 보면 iPhone 3gs 까지는 1pt 당 1픽셀이 할당되어 그대로 표시되는반면,
iPhone 4 부터 Retina Display 를 지원하는 기기의 경우, 2배율이 적용되어 1pt 당 4픽셀(2x2)을 가지게 된다.
iPhone X 부터 Retina HD Display 를 지원하기 시작하면서, 3배율이 적용되어 1pt  당 9픽셀(3x3)을 가지게 되었다.

<img width="1224" alt="Scale_Factor" src="https://github.com/GuTaeHo/Selection/assets/63102954/39e16c56-d753-4c9b-8dd6-929514d63d12">


</br>

### 정리
다양한 화면 해상도에 적용하기 위해서 iOS 시스템에서 권장하는 옵션이다.
여러 해상도가 적용된 이미지를 제공함으로써, 일관된 이미지 품질을 보장할 수 있다.
`Image Set` 에 1x, 2x, 3x 은 각각 `original`, `Retina`, `Retina HD` 가 적용된 기기에 맞게 자동으로 적용되는 이미지이다.


Point > Pixel 변환 도표 링크 - https://www.paintcodeapp.com/news/ultimate-guide-to-iphone-resolutions

</br>


## 참고
- https://m.blog.naver.com/iam_joanne/221928817478 (Point)
- https://dev-dream-world.tistory.com/189 (Pixel 과 Point: Scale Factor 도표 포함)
- https://developer.apple.com/documentation/uikit/uiscreen/1617836-scale (scale)
- https://ko.wikipedia.org/wiki/%EC%8A%A4%EC%BC%80%EC%9D%BC_%ED%8C%A9%ED%84%B0 (위키백과, scale factor)

## 이미지 출처
- https://www.paintcodeapp.com/news/ultimate-guide-to-iphone-resolutions

