# LLDB Command

LLDB 명령어 및 효과적이고 자주 사용되는 디버깅 방법 정리

</br>

## po (Print Object)

객체의 형태(설명)을 출력하는 명령어

</br>

### 레이아웃 충돌 시 주소로 어떤 뷰인지 찾는 방법

View Hierarchy 를 실행한 뒤,
`po` 명령으로 주소에 해당하는 뷰의 특성을 알 수 있다.

```lldb
# ex) 충돌난 뷰의 주소가 "0x11bf90f00" 일 때
po 0x11bf90f00

# ex) 충돌난 뷰의 부모 뷰 획득
po [0x11bf90f00 superview]

# ex) 충돌날 뷰의 전체 뷰 트리 출력
po [0x11bf90f00 recursiveDescription]
```


</br>
</br>

## frame

현재 스택 프레임의 정보를 출력하는 명령어.

특정 함수의 이름, 파일명 및 라인 번호, 지역 변수 등을 출력할 수 있다.

</br>

### 스택 프레임 지역 & 멤버 변수 출력

```lldb
frame variable

# or 

fr v
```

### 스택 프레임 정보 출력

```lldb
frame info

# or

fr i
```

</br>
</br>

## bt (backtrace)

현재 스레드의 콜 스택을 출력하는 명령어.

</br>

### 현재 스레드의 콜 스택 출력하기

브레이크 포인트가 걸렸을 때, Xcode 상의 Debug Navigator 에서 백트레이스를 볼 수 있지만,  
클로저(특히 @escaping)의 경우 추적이 잘 안되는 경우가 있다.

이 때, `LLDB` 명령어로 현재 스레드의 백트레이스를 찍어볼 수 있다

```lldb
thread backtrace

# or

bt
```