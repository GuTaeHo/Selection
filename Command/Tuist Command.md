# Tuist Command
`tuist` 에서 자주 사용되는 명령어 정리


<br>

## tuist install 
### 종속성 설치
`Package.swift 파일에 명세된 url 의 패키지를 내려받는다.  

최초 설치라면 명세된 버전 중 가장 높은 버전을, 이미 설치가 되었다면  
Package.resolved 에서 캐싱된 버전을 내려받는다.   


> 코코아 팟의 `pod install` 이 podfile.lock 에 명세된 종속성을 내려받는 것과 동일하다.

<br>

```bash
tuist install
```

<br>

### 종속성 업데이트
새로운 종속성을 내려받는다.
> `pod update` 와 동일하게 캐싱된 버전을 지우고, 완전히 새로운 종속성을 설치한다.

```base
tuist install --update
```

<br>