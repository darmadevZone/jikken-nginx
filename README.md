# Nginx, Nextjsの連携 (with`docker`)

- プロキシサーバーの利用
- `https`のやり方
- `nginx.conf`の設定
- https://nginx.org/en/docs/

## SSL 化で準備するもの

- 秘密鍵
    ```
    openssl genrsa -aes128 2048 > ./nginx/ssl/server.key
    ```

    - genrsa：RSA形式の秘密鍵を作成する
    - -aes128：128ビットのAES形式で暗号化
    - 2048：2048バイト長の鍵を作成


- CSR(証明書署名要求)

    ```
    openssl req -new -key ./nginx/ssl/server.key > ./nginx/ssl/server.csr
    ```

    - req：CSRファイルを作成する際に指定
    - -new：CSRを新規作成
    - -key：秘密鍵ファイルを指定

- CRT(SSL サーバー証明書)

    ```
    openssl x509 -in ./nginx/ssl/server.csr -days 365 -req -signkey ./nginx/ssl/server.key > ./nginx/ssl/server.crt
    ```

    - x509：X.509形式の証明書を発行
    - -in server.csr：CSRファイルを指定
    - -days 365：証明書の有効期限を指定
    - -req：入力ファイルがCSRファイルであることを示す
    - -signkey server.key：秘密鍵ファイルを指定

---

### 参考

- [`Nginx`でHttps 化](https://www.engilaboo.com/nginx-docker-https/#google_vignette)
- https://nginx.org/en/docs/


---

### `nginx.conf`の全体的な設定

```
server {
  server_name localhost;

  listen 80;
  listen [::]:80;

  location / {
    proxy_pass http://front-app:3000;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection 'upgrade';
    proxy_set_header Host $host;
    proxy_cache_bypass $http_upgrade;
  }

  
  access_log /var/log/nginx/access.log;
  error_log  /var/log/nginx/error.log;
  error_page 404             /404.html;
  error_page 505 502 503 504 /500.html;

  client_max_body_size 300m;
  keepalive_timeout 5;
}

ssl_session_cache   shared:SSL:10m;
ssl_session_timeout 10m;

server {

  server_name localhost;
  listen 443 ssl;
  listen [::]:443 ssl;

  location / {
    proxy_pass http://front-app:3000;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection 'upgrade';
    proxy_set_header Host $host;
    proxy_cache_bypass $http_upgrade;
  }

  keepalive_timeout 70;
  ssl_certificate /etc/nginx/ssl/server.crt;
  ssl_certificate_key /etc/nginx/ssl/server.key;
  ssl_password_file /etc/nginx/ssl/server.password;
  ssl_protocols       TLSv1.2 TLSv1.3;
  ssl_ciphers         HIGH:!aNULL:!MD5;
}

```
