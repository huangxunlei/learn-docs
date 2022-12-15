网络配置
[网络配置地址](https://www.cnblogs.com/renshengruxi/p/11474359.html)



```bash


# 1、查看服务器所有被占用端口

netstat -ant

# 2、验证某个端口号是否被占用

netstat -tunlp | grep 端口号

# 3、查看所有监听端口号

netstat -lntp
```

