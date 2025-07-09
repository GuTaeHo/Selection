# express.js & nginx 로 fcm 보내기 (+ pm2 로 node 프로세스 관리)

라즈베리파이 OS(데비안 기반) 환경에서 Node.js의 Express 프레임워크와  
Nginx 리버스 프록시를 활용하여 RESTful API 서버를 구축한 과정 설명

</br>
</br>

## node js 설치

현재 Raspberry PI OS **Bookworm** 에서 API 가 구축될 예정이지만,  
해당 배포판의 `apt` 저장소에서는 최신 node 를 지원하지않는다.

`NodeSource PPA` 는 node.js 의 최신버전을 설치할 수 있도록 공식적으로  
제공하는 외부저장소를 관리한다.

더 높은 장기지원 버전이 포함된 외부 저장소를 추가하고 설치를 진행한다.

</br>

```bash
# 가장 최신 lts 버전의 node 가 존재하는 apt 저장소 추가
curl -fsSL https://deb.nodesource.com/setup_lts.x | sudo -E bash -
# node 설치
sudo apt install -y nodejs
```

</br>

### node & npm 설치 확인

```bash
node -v
npm -v
```

</br>
</br>

## express 설치

```bash
npm install express
```

위 명령 수행 후, 실행될 소스 파일 위치시키고 실행해보자

</br>

### API 실행

```bash
node index.js
```

</br>
</br>

## 에러처리

node express 는 3000번 포트를 사용하지만, 라우터는 3000번으로 포트포워딩되어있지 않아 (ERR_CONNECTION_REFUSED) 에러가 발생할 수 있다

3000 번 포트를 개방하면 바로 사용가능 하지만 일반적(서비스 환경)으로 이렇게 쓰지않고,  
앞 단에 nginx 같은 `프록시 서버`를 두어 중계하는 형태로 구성한다 (리버스 프록시)

</br>

### 리버스 프록시 (nginx -> express) 구성 방법

1. `nginx` 설정 파일로 이동

    ```bash
    vi /etc/nginx/sites-available/default   # 일반 배포판
    vi /etc/nginx/nginx.conf    # 라즈베리파이 OS
    ```

2. `include` 구문 경로 확인 후 이동

    ```bash
    include /etc/nginx/conf.d/*.conf;   # 확인

    ...

    vi /etc/nginx/conf.d/default.conf   # 이동
    ```

3. server 블록 내부에 아래 코드 추가

    ```bash
    location / {
            proxy_pass http://localhost:3000;	# 실질적인 전달(프록시)
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection 'upgrade';
            proxy_set_header Host $host;
            proxy_cache_bypass $http_upgrade;
        }
    ```

4. 저장 후 nginx 재시작

    ```bash
    sudo nginx -t
    sudo systemctl restart nginx
    ```

</br>

이제 클라이언트가 443 번 포트로 서버에 접근할 때, `nginx` 는 요청을 `express` 로 전달하여 작업을 처리한다.

</br>

### 정상동작 테스트

Post 로 send 엔드포인트에 body 를 포함한 요청을 보내보자

```bash
curl -X POST https://server.com/send \
     -H "Content-Type: application/json" \
     -d '{"title": "swifty", "body": "Hello, World!"}'
```

</br>
</br>

## pm2 로 백그라운드에서 서버 실행하기

터미널에서 수동으로 서버를 켰을 때, 터미널에서 다른작업을 하거나 종료되면 서버가 동작하지않는다.

백그라운드에서 api 서버를 동작시키기 위해서 `nohup` 명령어 또는 `pm2` 패키지 등을 사용할 수 있다.

노드 프로세스만을 관리할 수 있고, 모니터링이 가능한 `pm2` 를 사용해보도록하겠다

</br>

### pm2 설치

```bash
npm install -g pm2
```

</br>

### pm2 명령어

```bash
# 실행
pm2 start example.js    # 터미널이 종료돼도 `node` 가 계속 실행되는 것을 볼 수 있다

# 프로세스 목록 확인
pm2 list

# 재실행
pm2 restart example.js

# (앱 크래시 시) 자동 재시작
pm2 start example.js --watch

# 종료
pm2 stop example.js

# 로그 확인
pm2 logs
```
