# LLDB Command

LLDB 명령어 및 효과적이고 자주 사용되는 디버깅 방법 정리

</br>

## po

### 레이아웃 충돌 시 주소로 어떤 뷰인지 찾는 방법

View Hierarchy 를 실행한 뒤,
`po` 명령으로 주소에 해당하는 뷰의 특성을 알 수 있다.

```bash
# ex) 충돌난 뷰의 주소가 "0x11bf90f00" 일 때
po 0x11bf90f00

# ex) 충돌난 뷰의 부모 뷰 획득
po [0x11bf90f00 superview]

# ex) 충돌날 뷰의 전체 뷰 트리 출력
po [0x11bf90f00 recursiveDescription]
```

</br>
