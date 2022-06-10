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