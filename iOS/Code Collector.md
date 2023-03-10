### 코드 수집기

> 나중에 유용하게 사용할법한 소스코드 모음집

<br />

### 인덱스 (Command + f)

1. 테이블 뷰 높이 동적 변경 시
2. 테이블 뷰 셀 여백 변경 시
3. 키보드 유무에 따라 UI 변경 시
4. 제스쳐 진행 도중 다른 입력을 막을 때 (페이지 뷰 컨트롤러)
5. Xib 파일 인스턴스화 시
6. 인스턴스의 클래스 명을 String 형태로 얻고싶을 때

<br />

```swift
// 테이블 뷰 높이 동적 변경 시 사용가능 할 것으로 보임
override func viewWillLayoutSubviews() {
    super.updateViewConstraints()

    tableViewHeight.constant = tableView.contentSize.height
}
```

<br />

```swift
// 테이블 뷰 셀 여백 변경 시 사용
// 커스텀 셀 하위에 아래 메소드 오버라이딩
// 기존 프레임 사이즈를 인셋을 적용한 프레임 사이즈로 변경하는게 키 포인트
override func layoutSubviews() {
    super.layoutSubviews()
		let insets = UIEdgeInsets(top: 10, left: 10, bottom: 10, right: 10)
    contentView.frame = contentView.frame.inset(by: insets)
}
```

<br />

```swift
// 노치 유무, 키보드 유무에 따라 UI 변경 시 사용
// 키보드 표시 중일 때
/// - parameter: keyboardHeight (키보드 현재 높이)
/// - parameter: keyboardHeight (하단 SafeArea 영역 사이즈)
UIView.transform = CGAffineTransform(translationX: 0, -(keyboardHeight - bottomSafeAreaSize))
// 키보드 미표시 시
UIView.transform = .identity

// MARK: AffineTransform 의 경우 스크롤 뷰가 포함된 뷰에선 사용이 힘듦
// 아래는 제약 조건 수정을 통해 UI 변경 시 사용 (스냅킷 사용)
UIView.animate(withDuration: 0.1) { [self] in
		btNext.snp.remakeConstraints {
      	$0.bottom.equalToSuperview().inset(keyboardHeight)
    }
   	/// - warning: 상위 뷰를 다시 그려줘야 정상적인 애니매이션이 표시됨
		view.layoutIfNeeded()
}
```

<br />

```swift
/* BasePageViewController 생성 후 아래코드 구현해보기 */

// 이전 뷰 컨트롤러 표시
func pageViewController(_ pageViewController: UIPageViewController, viewControllerBefore viewController: UIViewController) -> UIViewController? {
    if let viewController = shopListPageViewController?.pageViewAdapter.pageViewController(pageViewController, viewControllerBefore: viewController) as? ShopListContentViewController {
        // 목록 갱신을 위해 플래그 변수 초기화
        viewController.isFirstLoad = true
        return viewController
    }

    return nil
}

// 다음 뷰 컨트롤러 표시
func pageViewController(_ pageViewController: UIPageViewController, viewControllerAfter viewController: UIViewController) -> UIViewController? {
    if let viewController = shopListPageViewController?.pageViewAdapter.pageViewController(pageViewController, viewControllerAfter: viewController) as? ShopListContentViewController {
        // 목록 갱신을 위해 플래그 변수 초기화
        viewController.isFirstLoad = true
        return viewController
    }

    return nil
}

// 카테고리 레이아웃 업데이트
func pageViewController(_ pageViewController: UIPageViewController, didFinishAnimating finished: Bool, previousViewControllers: [UIViewController], transitionCompleted completed: Bool) {

    categoryCollectionView.isUserInteractionEnabled = true
    // 현재 보이는 뷰 컨트롤러 획득
    guard let shopListViewController = pageViewController.viewControllers?.first as? ShopListContentViewController else { return }
    // (어댑터에 저장된) 뷰 컨트롤러 인덱스 획득
    if let currentIndex = shopListPageViewController?.pageViewAdapter.getItems().firstIndex(of: shopListViewController) {
        categoryCollectionViewAdapter.changeSelectedCategoryLayout(collectionView: categoryCollectionView, position: currentIndex)
        categoryCollectionView.scrollToItem(at: IndexPath(item: currentIndex, section: 0), at: .centeredHorizontally, animated: true)

        let item = categoryCollectionViewAdapter.getItem(index: currentIndex)
        lbTitleCategory.text = item.title
    }
}

// 제스쳐 진행 도중 다른 입력을 막을 때 (페이지 뷰 컨트롤러)
func pageViewController(_ pageViewController: UIPageViewController, willTransitionTo pendingViewControllers: [UIViewController]) {
    /*
     페이지간 전환을 매우 빠르게 할 경우, pageViewController(didFinishAnimating) 메소드가 호출되지 않는 경우를 확인하였음,
     self.view.isUserInteractionEnabled = false 는 지양할 것 (화면전체가 먹통됨) */
    categoryCollectionView.isUserInteractionEnabled = false
}
```

<br />

```swift
// Xib 파일 인스턴스화 시 사용
extension UIView {
    static func loadFromNib<T>() -> T? {
        let identifier = String(describing: T.self)
        let view = Bundle.main.loadNibNamed(identifier, owner: self, options: nil)?.first
        return view as? T
    }
}

// 사용법
/// - note: MyXibCustomView 참조
if let customView: CustomView = UIView.loadFromNib() {
  // do something
}
```

<br />

```swift
// 인스턴스의 클래스 명을 String 형태로 얻고싶을 때...
let className = String(describing: type(of: self))
```

