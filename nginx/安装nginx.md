- 下载nginx

  见官网(http://nginx.org/en/linux_packages.html#RHEL-CentOS)

- yum 安装

  更新yum 或安装yum

```
sudo yum install yum-utils
```

启动nginx 

```
systemctl start nginx.service
```





# nginx 反向代理



```bash
server {
        listen       80;
        server_name  docker.huangxunlei.cn;
        set $x $remote_addr;
        if ($http_ali_cdn_real_ip) {
                set $x $http_ali_cdn_real_ip;
        }

   location / {
        proxy_pass http://127.0.0.1:2375;
        proxy_set_header  Host  $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header REMOTE-HOST $remote_addr;
        client_max_body_size    100m;
        set $allow true;
            if ($http_x_forwarded_for !~ "xxx.xxx.xxx.xxx|yyy.yyy.yyy.yyy") {
                set $allow false;
            }
            if ($allow = false) {
                return 403;
            }

    }
}
```

## nginx 反向代理去掉请求时候的匹配地址

增加 rewrite 的替换操作

```bash
  location /app-api/ {
        rewrite ^/app-api/(.*)$ /$1 break;
        proxy_pass http://127.0.0.1:9372;
        proxy_set_header Host $Host:$server_port;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header REMOTE-HOST $remote_addr;        proxy_set_header X-Host $host:$server_port;
        proxy_set_header X-Scheme $scheme;
        proxy_connect_timeout 30s;
        proxy_read_timeout 86400s;
        proxy_send_timeout 30s;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }
```



