# zerotier-planet
zerotier planet

## X API
https://docs.zerotier.com/api/service/ref-v1/
```code
docker compose exec planet /bin/sh

TOKEN=$(cat /var/lib/zerotier-one/authtoken.secret)
curl -H "X-ZT1-Auth: $TOKEN" http://localhost:9994/status
```
## Caddyfile
https://caddyserver.com/api/download?os=linux&arch=amd64&idempotency=17441241515868
```code
:1979 {
    reverse_proxy /* http://localhost:9994 {

    }
}
```
## A Planet Server
https://github.com/xubiaolin/docker-zerotier-planet
## B SideCar SpeedTesting
```code
  openspeedtest:
    image: openspeedtest/latest
    #restart: always
    cap_add:
      - NET_ADMIN
    stdin_open: true
    tty: true
    network_mode: host
    #ports:
    #  - "9993:9993"
    #  - "9993:9993/udp"

  zerotier:
    image: zerotier/zerotier
    #network_mode: host
    cap_add:
      - NET_ADMIN
    devices:
      - /dev/net/tun
    volumes:
      #- ./zerotier/dist/planet:/var/lib/zerotier-one/planet
      - ./zerotier-one:/var/lib/zerotier-one
    command:
      - 677a4f5bc655fd0a
    depends_on:
      - openspeedtest
    network_mode: "service:openspeedtest"
```

```code
  kuma:
    image: louislam/uptime-kuma:1
    restart: always
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - ./kuma:/app/data
    stdin_open: true
    tty: true

location ~ ^/(upload/logo1.png|status/abc|assets/index|assets/zh-CN|api/status-page/)  {
           proxy_pass http://kuma:3001;
           proxy_set_header X-Real-IP $remote_addr;
           proxy_set_header Host $host;
           proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
           proxy_http_version 1.1;
           proxy_set_header Upgrade $http_upgrade;
           proxy_set_header Connection "upgrade";
}
```
## C VLAN
https://github.com/crazygit/family-media-center/blob/master/docker-compose.openwrt.yml

## D Clients
https://www.zerotier.com

https://ipv4.icanhazip.com

```code
upstream zerotier {
  server 127.0.0.1:3443;
}

server {

  listen 443 ssl;

  server_name {CUSTOME_DOMAIN}; #替换自己的域名

  # ssl证书地址
  ssl_certificate    pem和或者crt文件的路径;
  ssl_certificate_key key文件的路径;

  # ssl验证相关配置
  ssl_session_timeout  5m;    #缓存有效期
  ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;    #加密算法
  ssl_protocols TLSv1 TLSv1.1 TLSv1.2;    #安全链接可选的加密协议
  ssl_prefer_server_ciphers on;   #使用服务器端的首选算法


  location / {
    proxy_pass http://zerotier;
    proxy_set_header HOST $host;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}

server {
    listen       80;
    server_name  {CUSTOME_DOMAIN}; //替换自己的域名
    return 301 https://$server_name$request_uri;
}
```


./data/zerotier/dist 目录下有个 planet和moon 文件

admin/password
