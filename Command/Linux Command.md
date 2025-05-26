## 연결

**SSH 키 생성 (비밀번호 없이 연결)**
- 참고: https://pyromaniac.me/24
> **ssh-keygen** 명령을 통해 공개키와 비밀키 쌍을 생성할 수 있다
> `-t`: 키 인증 알고리즘 (rsa, dsa, ecdsa)
> `-b`: 키 비트 수
> `-f`: 키 파일 명 설정
>
> 생성 후 `.pub` 으로 끝나는 확장자가 공개키(Public Key) 나머지가 개인키(Private Key) 이다.

```zsh
// RSA 알고리즘을 사용하며, 길이가 4096 비트고, 이름이 myKey 인 SSH 키를 생성한다.
ssh-keygen -t rsa -b 4096 -f mykey
```

**SSH 키 전송**
> 개인 키는 클라이언트가, 공개 키는 서버가 가지고 있어야하므로, 생성된 키를 서버로 전달해야한다.
> **ssh-copy-id** 명령을 통해 키를 전달할 수 있다.
>
> 전달된 키는 홈 디렉토리의 `.ssh/authorized_keys` 파일(root는 `etc/.ssh`)에서 찾아볼 수 있다.

```zsh
ssh-copy-id -i <공개키 경로> <서버 IP>
```

**SSH 접속**
ssh `사용자 명`@`원격지 IP`

</br>

## 시스템
**시스템 종료**
```zsh
shutdown -h now
```

**시스팀 재시작**
```zsh
shutdown -r now
```

**프로세스 정보**
```zsh
// 시스템 정보를 포함한 프로세스 목록 출력
top
// 데몬 상태 출력
systemctl status
```

**시스템 하드웨어 정보(lshw 설치필요)**
```zsh
// 상세 정보 출력
lshw
// 간소화된 정보 출력
lshw -short
// CPU 온도
vcgencmd measure_temp
// 메모리 출력
free
// 디스크 출력
df -h
```

**현재 운영체제 확인**
```zsh
lsb_release -a
```

</br>

## 패키지 관리자
**저장소 관리**
> Ubuntu 및 Debian 기반의 소프트웨어 리포지토리는 **/etc/apt/sources.list/** 디렉토리 아래의 별도의 파일로 정의된다
> 파일을 직접 수정할 수도 있지만, apt-add-repository 명령을 통해 레포지토리를 추가, 수정 및 삭제할 수 있다. (단, **software-properties-common** 패키지 설치 필요)
```zsh
// 저장소 목록 출력
add-apt-repository -L
// 저장소 추가
add-apt-repository [options] <저장소 명>
// 저장소 제거
add-apt-repository --remove <저장소 명>
```
**저장소 업데이트**
```zsh
apt update
```

**패키지(앱) 업데이트**
```zsh
apt upgrade
```

**설치된 패키지 리스트 출력**
```zsh
apt list
```

**설치된 업그레이드 가능한 패키지 리스트 출력**
```zsh
apt list --upgradable
```

**저장소에서 설치가능한 패키지 검색**
```zsh
apt search <패키지명>
```

## 네트워크
**현재 IP**
```zsh
hostname -I
```



