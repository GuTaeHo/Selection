# Xcode Enhanced
Xcode 를 조금 더 생산적이며 효율적으로 사용할 수 있는 방법 정리

<br>

**디버그 연결중단 개선**

![Debugger lost connection](../Resource/Image/Command/imgLostConnectionDebugger.png}

1. 홈 디렉토리에 .lldbinit 파일을 생성한다.
```bash
vi ~/.lldbinit
```
2. 다음 라인을 파일에 추가한다.
```bash
settings set plugin.process.gdb-remote.packet-timeout 300
```
3. Xcode 를 재실행한다.

참고
[Lost connection to the debugger - apple forum](https://forums.developer.apple.com/forums/thread/681037)

<br>

**디버깅 속도 개선**
```bash
vi ~/.lldbinit
```
```bash
settings set target.experimental.swift-enable-cxx-interop false
```

참고
[why lldb so painfully slow - stackoverflow](https://stackoverflow.com/questions/75850606/why-is-lldb-so-painfully-slow)
