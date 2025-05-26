# PhotoKit

**PhotoKit** 은 사용자 사진 앱의 이미지 및 비디오 에셋에 접근할 수 있도록 Apple 에서 제공하는 API 다.

</br>

**PhotoKit API** 는 두 개의 프레임워크를 제공한다

- Photos
사진 앱에 쿼리하여 앨범 정보 및 사진 정보를 가져오거나 편집하는 기능을 제공하며, 
기기뿐만 아니라 iCloud 에 업로드 되어있는 에셋에도 접근 가능하다.
또, 대량의 이미지를 위한 캐싱 기능도 제공한다.
    
- PhotosUI
커스텀 포토 피커가 필요하지 않거나, 빠르게 포토 피커 기능이 필요할 경우에 사용가능한 
`PHPickerViewController` (UIKit), `PhotosPicker` (SwiftUI) 를 제공한다.

</br>
</br>


## Photos


### 접근 권한 획득

`PhotosUI` 를 사용하지 않고, 커스텀 포토 피커를 개발할 경우, 
사용자의 사진에 접근하기 위해 먼저 권한을 획득해야한다.

**권한 요청 옵션**

- addOnly: 사진 앱에 저장(쓰기) 하는 기능 만 요청 (읽을 수 없음)
- readWrite: 사진 앱에 저장(쓰기) 및 불러오기(읽기) 를 모두 요청

**코드**
```swift
PHPPhotoLibrary.requestAutorization(for: .readWrite) { status in
    // TODO: status 분기 처리
}
```

</br>

**권한 종류**


1-1. 권한 요청 시
<img src="../Resource/Image/iOS/IOS_PhotoKit_Auth1.jpg" width="50%">

1-2. 설정앱
<img src="../Resource/Image/iOS/IOS_PhotoKit_Auth2.jpg" width="50%">

- notDetermined: 아직 결정되지않음 (요청한 적 없는 경우)
- restricted: 제한됨 (보호자 관리 나 스크린타임 등에 의한 시스템에서 막은 경우)
- denied: 허용 안 함
- authorized: 전체 접근 허용
- limited: 일부 접근 허용 (제한된 접근, 사용자가 선택한 에셋)

</br>

### 앨범목록에서 앨범 에셋 가져오기

</br>

### 앨범에서 사진 에셋 가져오기

</br>

### 이미지 요청 시 주의사항

- targetSize
- reuseable cell

### 사진 앱 변경 사항 옵져빙

새로운 사진이 등록되었을 경우, 사진 목록을 업데이트하는 기능이 필요할 수 있는데,

Photos 프레임워크에서는 `PHPhotoLibraryChangeObserver` 를 제공한다

PHPhotoLibraryChangeObserver 의 `photoLibraryDidChange(changeInstance:)` 메소드는 
새로 추가된 이미지가 있을 경우 호출되는 메소드다

사용 방법은 아래와 같다

```swift
// 1. delegate 등록
PHPhotoLibrary.shared().register(self)

// 2. delegate 채택 및 메소드 구현
extension CustomerPickerViewController: PHPhotoLibraryChangeObserver {
    func photoLibraryDidChange(_ changeInstance: PHChange) {
        // TODO: 사진 앱 업데이트 시 작업
    }
}
```