## 1、启动Redis

```bash
src/redis-server   redis.conf &
```

## 2、Redis 安装

[官网下载地址](https://redis.io/download)

```bash
#解压
下载 wget https://download.redis.io/releases/redis-6.0.9.tar.gz

#解压
tar xzf redis-6.0.9.tar.gz

cd redis-6.0.9
# 安装  检查是否已经添加的了gcc 编译器  yum install -y gcc g++ gcc-c++  
make
```

## 3、启动

```bash
 src/redis-server redis.conf &
 # 加 & 表示后台启动
```

## 4、远程连接

```bash
## 修改配置

vi redis.conf
# bind 127.0.0.0  bind 0.0.0.0
#  protected mode yes 改为  protected mode no
```

