# 安装

 Linux-Centos 环境下安装并启动 elasticsearch 7.17.7

## 1 下载软件包

```shell
# 下载安装包
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.17.7-linux-x86_64.tar.gz
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.17.7-linux-x86_64.tar.gz.sha512
shasum -a 512 -c elasticsearch-7.17.7-linux-x86_64.tar.gz.sha512 
tar -xzf elasticsearch-7.17.7-linux-x86_64.tar.gz
cd elasticsearch-7.17.7/ 
```



> shasum 命令需要安装 perl-Digest-SHA
>
> ```shell
> yum install perl-Digest-SHA
> ```

## 2 添加 es 用户

 elasticsearch 不能以 root 用户启动，所以最好创建单独的用户

```shell
#添加用户组
groupadd esuser
# 添加 es 用户
adduser -g esuser esuser
# 切换到 esuser
su esuser
# 将 elasticsearch目录授权给esuser 否则无法正常启动
chown -R esuser:esuser elasticsearch-7.17.7
```

## 3 修改配置文件

文件路径/config/elasticsearch.yml

```shell
cd config
vim elasticsearch.yml
```

```yaml
#节点名称
node.name: node-1
# 数据存储目录
path.data: data
# 日志存储目录
#path.logs: /path/to/logs

# 设置指定的本机 IP 地址进行访问(设置为 0.0.0.0 表示本节点的所有可用地址均可访问)
network.host: 0.0.0.0

# es 的访问端口 默认为9200
http.port: 9200

#discovery.seed_hosts: ["127.0.0.1", "[::1]"]

# 集群的初始主节点
# cluster.initial_master_nodes: ["node-1"]

#自动创建索引
#action.auto_create_index: .monitoring*,.watches,.triggered_watches,.watcher-history*,.ml*
# https://www.elastic.co/guide/en/elasticsearch/reference/7.16/configuring-stack-security.html
#是否启用安全登录，生成环境建议开启设置
xpack.security.enabled: false
# 服务方式 如果没有此项那么前面 discovery.seed_hosts 和  cluster.initial_master_nodes 必须配置
discovery.type: single-node
# 取消geoip 索引
ingest.geoip.downloader.enabled: false
# 服务内集群的访问端口号 默认为9300
transport.tcp.port: 9301
```

## 4 启动服务

```shell
# 后台启动服务
./bin/elasticsearch -d -p pid
# 会生成一个pid文件
#关闭服务
pkill -F pid
```

 

## 5.启动问题

### 5.1 问题1

> [1]: max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]

使用sudo用户修改文件：`vim /etc/sysctl.conf`

添加一行配置：

`vm.max_map_count=655360`

验证命名：

`sudo sysctl -p`

### 5.2 问题2

> [2]: the default discovery settings are unsuitable for production use; at least one of [discovery.seed_hosts, discovery.seed_providers, cluster.initial_master_nodes] must be configured

放开：

```yaml
cluster.initial_master_nodes: ["node-1"]
#或者 增加 选其一
discovery.type: single-node
```

## 5.3 安全配置 

```
#配置项开启
xpack.security.enabled: true
```

> 自动生成密码命令

> ```shell
> ./bin/elasticsearch-setup-passwords auto
> ```

> 手动输入密码
>
> ```
> ./bin/elasticsearch-setup-passwords interactive
> ```
