# Flutter Distribution

</br>

## Web 배포

Android Studio 기준으로 작성됨

```sh
배포 환경
OS: Debian GNU/Linux, raspberrypi 6.12.20+rpt-rpi-v8
Web Server: Nginx
Application Server: Node.js + Express
```

</br>
</br>

1. 프로젝트 파일의 `project` > `build` > `web` > `index.html` 파일 열기

만약 루트(/) 주소에 web 폴더가 위치되어 있지 않다면 아래와 같이 변경

```html
<!-- /relase/playground/web/ 아래에 파일이 있는 경우 -->
<base href="/release/playground/web">
```

</br>

2. `Android Studio` > Web 설정 후 한번 실행

</br>

3. 프로젝트 파일의 `project` > `build` > `web` 디렉토리 복사

</br>

4. `/etc/nginx/conf.d/default.conf` 열기

</br>

5. `server` 블록 아래 원하는 웹 접속 경로 작성

서비스를 `https://domain.com/release/playground/index.html` 접근 시 제공하고 싶은 경우

```sh
server {
    ...

    # ex) domain.com/release/playground/index.html 으로 접근 시
    location /release/playground {
        alias /usr/share/nginx/html/release/playground/web;
        index index.html;
        try_files $uri $uri/ $uri/index.html =404;
    }

    ...
}
```

</br>

6. 설정 파일 문법 검사

```sh
sudo nginx -t
```

</br>

7. 문법이 정상일 경우, 다운타임 없이 재실행

```sh
sudo nginx -s reload
```