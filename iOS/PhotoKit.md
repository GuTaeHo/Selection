# PhotoKit

**PhotoKit** 은 사용자 사진 앱의 이미지 및 비디오 에셋에 접근할 수 있도록 Apple 에서 제공하는 API 다.

</br>

### 숙지사항

1. 애플은 사용자 정보를 민감하게 처리하기 때문에, 사진같은 미디어에 접근하기 위해서 반드시 사용자로 부터 반드시 권한을 획득해야한다.

2. 사용자는 앱에게 특정한 사진만 접근 가능하도록 사진마다 권한을 제공할 수 있으며, `PhotoKit` 은 허용된 사진만 접근이 가능하다

3. `PhotoKit` 은 사진과 앨범정보를 즉시 가져올 수 없다.  
    앨범에 대한 메타 정보인 `PHAssetCollection` 과 사진에 대한 메타 정보인 `PHAsset` 을 먼저 획득한 다음,  
    `PHAsset` 의 정적 메소드인 `fetchAssets` 을 통해서 `UIImage` 형태로 사진을 가져올 수 있다

4. `PHAssetCollection` 과 `PHAsset` 은 원본에 대한 메타 데이터를 가지고 있다.  
    메타 정보는 원본 사이즈(`pixelWidth` & `pixelHeight`),
미디어 타입(`image` & `video` ...), 일자(`creationDate` & `modificationDate` ...)

</br>

**PhotoKit API** 는 두 개의 프레임워크를 제공한다

- Photos
사진 앱에 쿼리하여 앨범 정보 및 사진 정보를 가져오거나 편집하는 기능을 제공하며, 기기뿐만 아니라 iCloud 에 업로드 되어있는 에셋에도 접근 가능하다.
또, 대량의 이미지를 위한 캐싱 기능도 제공한다.
- PhotosUI
커스텀 포토 피커가 필요하지 않거나, 빠르게 포토 피커 기능이 필요할 경우에 사용가능한 `PHPickerViewController` (UIKit), `PhotosPicker` (SwiftUI) 를 제공한다.

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

### 앨범목록에서 에셋 콜렉션(앨범) 가져오기

앨범은 크게 3 종류가 있다.

- album: 사용자가 직접 만든 앨범
- smartAlbum: 시스템이 자동으로 만든 앨범
- moment: 특정 시간을 기준으로 시스템이 자동으로 만든 앨범 (iOS 8부터 제공, iOS 13에서 deprecated 됨)

`album` 과 `smartAlbum` 의 에셋 콜렉션을 가져오는 코드는 다음과 같다

</br>

```swift
// 1. fetchAssetCollections 메소드를 사용해 PHFetchResult 로 가져온다
let result = PHAssetCollection.fetchAssetCollections(with: .album, subtype: .any, options: options)

// 2. PHFetchResult<PHAssetCollection> 를 enumerateObjects 메소드로 콜렉션을 추출한다
var collections: [PHAssetCollection] = []
result.enumerateObjest { (collection, index, stop) in
    collections.append(collection)
}

return collections
```

</br>

### 에셋 콜렉션 (앨범)에서 에셋 (사진) 가져오기

에셋을 가져오는 방법에는 여러 방식을 지원한다.

- 모든 에셋 가져오기 (에셋 콜렉션 필요 X)
- 특정 미디어 타입(`PHAssetMediaType.image`)만 가져오기
- 특정 앨범(`PHAssetCollection`) 에서 가져오기

```swift
// 1. 모든 에셋 가져오기
var assets: [PHAsset] = []
PHAsset.fetchAssets(with: nil).enumerateObjects { asset, index, stop in
    assets.append(asset)
}

return assets

// 2. 특정 미디어 타입만 가져오기
PHAsset.fetchAssets(with: .image).enumerateObjects { asset, index, stop in
    assets.append(asset)
}

return assets


// 3. 특정 앨범에서 가져오기
PHAsset.fetchAssets(in: collection).enumerateObjects { asset, index, stop in
    assets.append(asset)
}

return assets
```

</br>

### 에셋 (사진) 에서 UIImage 가져오기

`Photos` API 는 `PHAssets` 의 이미지를 가져오기 위한 Manager 클래스를 제공하며 그 종류는 아래와 같다

</br>

- PHImageManager  
    에셋에서 이미지를 가져오기 위한 기본적인 클래스.  
    싱글톤 객체(PHImageManager.default() 로 접근)를 지원한다
- PHChachingImageManager
    이미지를 미리 캐싱해두고 불러올 수 있는 클래스.
    이 클래스는 싱글톤을 지원하지 않기 때문에, 별도의 객체를 생성해서 사용.

</br>

이미지를 가져오기전 필요한 준비물은, `PHAsset`, `targetSize`, `contentMode` 가 있다.

`targetSize` 는 가져올 이미지의 사이즈를 지정할 수 있고,  
`contentMode` 는 `targetSize` 로 지정한 사이즈에 맞게  
원본 이미지의 비율과 크기를 어떻게 정할건지에 대한 모드를 지정할 수 있다.

</br>

```swift
var images: [UIImage] = []

// 1. ImageManager 에서 원본 UIImage 가져오기
let targetSize = CGSize(width: asset.pixelWidth, height: asset.pixelHeight)

PHImageManager.default().requestImage(for: asset, targetSize: targetSize, contentMode: .aspectFill) { (image, _) in
    if let image {
        images.append(image)
    }
}

// 2. 캐시된 UIImage 가져오기
// TODO:




```

</br>

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

</br>

### 이미지 요청 시 주의사항



**Fetch Option (PHFetchOptions)**

시스템(`Photos`) 에서 

**Request Option (PHImageRequestOptions)**

`Asset` 에서 `UIImage` 를 가져오기 위해 사용되는 옵션.  
- 동기 or 비동기적으로 조회
- iCloud 에 저장된 경우 네트워크 접근 허용 여부
- 정렬 방식
-


**targetSize**



</br>

**reuseable cell**

</br>