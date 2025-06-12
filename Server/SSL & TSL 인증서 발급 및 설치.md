# SSL & TSL 인증서 발급 및 설치

유니버셜 링크를 구현하기 전, `https 프로토콜` 이 적용된 웹 서버가 있어야 구현이 가능하다고한다.

`https` 를 사용하기 위해선 신뢰할 수 있는 루트 기관(CA) 에서 발급된 **SSL or TSL 인증서**가

웹 서버에 설치되어있어야한다.

</br>

(~~눈물을 머금고~~) 이 참에 한번 구현해보도록 하자

</br>

```
라즈베리파이에 `nginx` 웹 서버가 이미 설치되어있기 때문에 `nginx` 설치가 되어있고,

(라우터를 사용중일 경우) 웹 서버 사설 ip 로 80번 포트가 포트포워딩 되어있다는 가정하에 진행한다.

(데비안 기반) 라즈비안 OS를 사용중이기 때문에 `apt` 패키지 관리자를 사용한다.
```

</br>

## Nginx에 HTTPS 적용하는 방법

**무료**로 TLS 인증서를 발급해주는 Let's Encrypt 기관(CA 기관 중 하나)에서  
빠르게 인증서를 설치할 수 있는 오픈소스 도구 `certbot` 과  
자동으로 nginx 설정을 도와주는 `python3-certbot-nginx` 를 제공한다.

</br>

1. `certbot`, `python3-certbot-nginx` 설치

    ```bash
    sudo apt update
    sudo apt install certbot python3-certbot-nginx
    ```

2. 인증서 발급

    ```bash
    sudo certbot --nginx -d yourdomain.com
    ```

    발급과 `nginx` 에 인증서 설정까지 자동으로 수행한다
    (만료 전 알림 전달을 위해 이메일 입력을 요구할 수도 있다)

    </br>

    **주의사항**

    ```
    Could not automatically find a matching server block for muangs.kr. Set the `server_name` directive to use the Nginx installer.
    ```

    만약 위 에러가 표시된다면 **/etc/nginx/sites-available/** 아래에  
    (없다면 **/etc/nginx/conf.d/default.conf**)  
    `server_name` 필드에 도메인 네임을 적어주면 된다.
    
    ```bash
    # default.conf

    server {
        listen 80;
        # server_name localhost;
        server_name domain.com;
    }
    ```

    수정완료 후 `nginx` 를 재실행한다

    ```bash
    systemctl restart nginx
    ```

    `certbot` 은 nginx 구성 파일의 `server_name` 을 통해 nginx 의 설정을 자동으로 변경한다.

3. (필요시) 방화벽 열기

    **방화벽 열기**

    ```bash
    sudo ufw allow 'Nginx Full'
    ```

    `ufw` 명령을 찾을 수 없을 경우 아래 수행

    **ufw 설치유무 확인**

    ```bash
    dpkg -L ufw
    ```

    **ufw 설치**

    ```bash
    sudo apt install ufw
    ```

    **ufw 상태 확인**
    ```bash
    sudo ufw status
    ```

4. 인증서 자동 갱신 설정

    ```bash
    sudo crontab -e
    # 매일 자정 00:00 에 만료 임박한 인증서 자동 갱신 및 nginx 재시작
    0 0 * * * /usr/bin/certbot renew --quiet --post-hook "sudo systemctl restart nginx"
    # crontab 재시작
    sudo service cron restart
    ```

### 80 포트 접근 시 https 리다이렉트 방법

`nginx` 구성 파일 열기
```bash
vi /etc/nginx/conf.d/default.conf

...

# default.conf
server {
    listen 80;
    server_name domain.name;
    return 301 https://$host$request_uri;
}
```