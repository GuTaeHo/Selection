# ssh

ssh 연결 시 발생할 수 있는 문제점 및 해결방안, 관련 정보와 꿀팁

</br>
</br>

## ssh 연결하기

> 컴퓨터에 ssh 클라이언트가 설치되어있다고 가정하고 진행

</br>

#### 들어가기 앞서...
> ssh 서버는 기본적으로 `root` 계정을 원격접속을 차단하고 있다.  
*(보안을 위해 실제 서비스 중인 환경에서는 별도의 유저를 만들어서 사용하자)*  
~~(현직자 왈 혼난다고 함)~~  
`root` 원격 접속을 위해서 아래 경로에 sshd_config 파일 수정,  
ssh 서비스를 재 실행 해야한다.

```bash
vi /etc/ssh/sshd_config

... 

PermitRootLogin yes # no or yes
```

ssh 서비스 재 실행

```bash
systemctl restart sshd
```


</br>

### 패스워드로 연결하기

#### 들어가기 앞서...
> 보안을 위해 키 인증 방식만 채택한경우, 패스워드 로그인이 표시가 안될 수 있다.  
해결방법은 다음과 같다

```bash
vi /etc/ssh/sshd_config

... 

PasswordAuthentication yes # no or yes
```

**ssh 서비스 재 실행**

```bash
ssh user@host # ex) ssh root@muangs.kr
```

</br>

### 키로 연결하기 (= 비밀번호 없이 접속)

키 기반 연결은 공개키 기반 암호화 방식을 사용하고 있다.  
먼저 키를 만든 다음 **공개키**를 서버에, **개인키**를 클라이언트에 두면 별도의 로그인 과정없이 연결이 가능하다

1. `ssh-keygen` 을 사용해 키 쌍을 생성한다
    ```bash
    ssh-keygen -t ed25519 -C "testemail@example.com"
    ```

    > 이메일은 단지 식별용이므로 본인이 인식할 수 있는 뭐든 상관없다

2. 생성된 키를 클라이언트와 서버에 각각 넣는다
   
    > 공개키: 파일명 확장자에 **.pub** 가 붙어있다  
    개인키: 파일명에 아무것도 없다

    **클라이언트 측**  
    아래 경로에 **개인키**를 위치시킨다.   
    (키 생성 시점에 이미 해당위치에 있다)

    ```bash
    cd ~/.ssh/
    ```

    **서버 측**  
    아래 파일에 **공개키** 정보를 기입한다
    ```bash
    cd ~/.ssh/authorized_keys
    ```

    추가) 서버 공개키 업로드 명령어  
    ```bash
    ssh-copy-id user@host
    ```
    

3.  ssh 서비스를 재 실행한다.
    ```bash
    systemctl restart sshd
    ```

</br>
</br>

## known_hosts

### known_hosts?

ssh 원격 접속되었던 서버 정보를 가지고 있는 호스트 목록이 기록된 파일

목록에는 각 서버의 **공개키**가 등록되어있다.

**방명록** 과 유사하다고 생각하면 될 듯

</br>

### 무슨 용도?
SSH 접속할 떄 다음과 같은 문구를 본적이 있을 것 이다.

> @@@ WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED! @@@

다음에 접속할 때, 이전에 접속했던 서버가 맞는지 확인하기 위해 사용된다 

서버의 공개키가 변경되는 경우는 크게 4가지가 이싿