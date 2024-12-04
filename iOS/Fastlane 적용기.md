# Fastlane 적용기


<br>

## 프로파일에 기기등록
1. 등록할 기기의 `UUID` 를 얻기 위해 다음 명령어 실행 또는 Xcode > 연결된 기기 선택 > Manage Run Destinations 의 `Identifier` 확인
```
// 터미널
xcrun xctrace list devices

/** 출력
구태호의 MacBook Pro (00000004-ABCD-EFGH-AAAA-123456781234)
iPad Pro 5 (18.1) (00000003-000000000000000C)
구태호의 Apple Watch (10.6.1) (00000002-000000000000000B)
구태호의 iPhone 15 Pro (18.1) (00000001-000000000000000A)
*/
```

2. fastlane 명령으로 기기 등록
```
fastlane run register_device udid:"00000003-000000000000000C" name:"구태호의 iPad Pro 5"
```

3. 프로파일 갱신요청
```
match development --force_for_new_deices
```

## 단축키

```swift
// 현재 화면 새로운 탭으로 열기
Command + t

// 현재 파일 네비게이터에 표시
Command + Shift + j

// 상단 바 고정
Alt + Command + t
```


<br>