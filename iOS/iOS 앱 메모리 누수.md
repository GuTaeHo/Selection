# iOS 앱 메모리 누수

  

1. Escaping Closure 와 Closure Capture
2. iOS 의 메모리 관리기법
3. 강한참조와 약한참조, weak self 의 개념 (+ ARC)

<br>

## 1. Escaping Closure 와 Closure Capture

컴퓨터 프로그래밍 언어 디자인에서, **일급 객체**(first-class object) 또는 **일급 시민**이란 다른 객체들에 일반적으로 적용 가능한 연산을 모두 지원하는 객체를 가리킨다. 보통 함수에 인자로 넘기기, 수정하기, 변수에 대입하기와 같은 연산을 지원할 때 일급 객체라고 한다.

### Closure

스위프트에서는 클로저(Closure)라는 (다른 언어에서 익명함수라고도 부름) 이름없는 함수를 정의할 수 있다.
특징은 함수의 **인자로 전달**할 수 있고, **변수에 대입**할 수 있으며, **함수의 반환**으로 사용할 수 있다는 점이다.

```swift
let closure: () -> String = {
  let name = "Cocobean"
  return name
}

print(closure) // "Cocobean"
```

<br>

### Escaping Closure & Closure Capture

앱을 제작할 때 사용자의 입력을 받거나, 데이터베이스에 저장된 데이터를 얻을 때 등
특정한 시점(프로그래밍에서는 **비동기**라고 부름) 에 특정한 작업을 하고싶은경우가 흔하다.

이 특정한 시점에 하고 싶은 작업을 swift 에서는 **탈출 클로저**(Escaping Closure)라는 이름으로 지원한다.

swift 에서 클로저의 외부에 있는 변수(인스턴스)에 참조할 때, 원본 값이 사라져도 참조할 수 있도록 해당 값을 캡쳐하며,
이를 **Closure Capture** 라고 부른다.

```swift
class Account {
  var name = "Cocobean"
  
  let closure: () -> String = {
    // 외부의 값 캡쳐하여 클로저 내부에서 사용
    return self.name
  }
}

let account = Account()

print(account.closure())	// Cocobean
```

  
<br>
  

## 2. iOS 의 메모리 관리기법

iOS 앱은 ARC(Auto Reference Counting) 라는 방식으로 메모리를 관리한다.

이름 뜻 그대로 참조를 세어가며 인스턴스가 더 이상 참조되지 않을 때 자동으로 메모리에서 해제하는 방식을 사용하며, 안드로이드의 ART(=JVM)의 GC와는 다르게 런타임이 아닌 **컴파일 단계에서 실행**된다.

그렇다면 어떻게 아직 앱이 실행되지도 않았는데 실행시간에 필요없는 인스턴스를 파악해 제거할 수 있을까?

이는 앱의 컴파일단계에서 인스턴스의 할당(사용), 해제(더 이상 사용되지않음)에 대한 여부를 **자동으로** 코드 사이에 집어넣어 특정 시점에 더 이상 참조되지 않는다면 인스턴스를 제거하는 방식으로 동작한다.

  
<br>
  

## 3. weak self 의 개념 (+ ARC)

swift 에서는 변수선언 시 기본적으로 강하게(strong) 참조하며, weak 키워드를 사용하면 약하게 참조한다.



### weak self

일반적인 함수의 인자로 전달된 클로저는 함수가 종료되기전에 이미 실행되어 사라지지만, Escaping Closure 는
함수가 **종료되어도 사라지지않는다.** 만약 이 때 클로저 내부에서 외부에 있는 변수를 참조하면 문제가 발생한다.
예시를 보자.

<br>
  

```swift
class FooViewController: UIViewController {
  var bar: String?
  var closure: (() -> ())?
  
  override func viewDidLoad() {
    super.viewDidLoad()
  
    closure = {
      // bar 강한참조 발생!
      self.bar = "FooBar"
    }
    
    dismiss(animated: true)
  }
  
  deinit() {
    // 호출 X
    print("FooViewController deinit")
  }
}
```

<br>


위 코드에선 뷰 컨트롤러가 dismiss() 되어도 클로저 내부의 **강한참조**로 인해 "FooViewController deinit" 가 출력되지않는다.

해결방법은 아래와 같다.

<br>


```swift
...
closure = { [weak self] in
  // self(뷰 컨트롤러)를 약하게 참조!
  self?.bar = "FooBar"
}
...
```

<br>

왜 이런일이 발생한 것일까? 
위 코드를 예로들어 ARC 방식을 설명하자면,

1. FooViewController가 **생성될 때** reference count 1 증가
2. viewDidLoad() 내부의 closure 초기화 부분에서 **self 참조** 즉, reference count 1 증가
3. **dismiss**(animated: true) 호출로 reference count 1 감소
4. FooViewController 대한 reference count 가 1 남아있기 때문에 해제 X

  

**ARC는 참조 카운트가 0이되면 자동으로 객체를 메모리에서 해제시키지만, **
**카운트가 1이기 때문에 메모리에서 사라지지않아 deinit() 이 호출되지 않게된다**

  

weak 키워드를 사용해 closure 내부에서 FooViewController 에 대해 약하게(weak) 참조함으로써 참조 카운트를 올리지 않는다면 정상적으로 deinit() 이 호출되며 뷰 컨트롤러가 메모리에서 해제된다.

  
<br>
  

## 참고

https://ko.wikipedia.org/wiki/%EC%9D%BC%EA%B8%89_%EA%B0%9D%EC%B2%B4 - 일급 객체

https://babbab2.tistory.com/83 - closure, ARC

https://babbab2.tistory.com/164 - closure, @escaping

https://stackoverflow.com/questions/39618803/swift-optional-escaping-closure-parameter - optional closure 는 @escaping 으로 동작할까?

https://devsrkim.tistory.com/entry/Swift-%EB%A9%94%EB%AA%A8%EB%A6%AC%EB%A5%BC-%EC%B0%B8%EC%A1%B0%ED%95%98%EB%8A%94-%EB%B0%A9%EB%B2%95-Strong-Weak-Unowned - 강한참조와 약한참조, ARC



