# 离线二进制安装k8s



## 1、文件下载

https://github.com/kubernetes/kubernetes/releases

## 1、初始化服务器

### 关闭防火墙

### 关闭selinux、

### 关闭swapoff -a

```bash
systemctl stop firewalld
systemctl disable firewalld
selinux=disabled
swapoff -a
```

## 部署规划

### 1.单master 集群

 ####  一.集群规划

master 

 主机名:  k8s-master1

ip: 192.168.1.128

worker1

 主机名: k8s-node1

IP:192.168.1.127

worker1

主机名:k8s-node2

Ip :192.168.1.130

k8s 版本

安装方式:二进制离线安装

#### 二.初始化服务器

1.关闭防火墙

```bash
systemctl stop firewalld
systemctl disable firewalld
```

2.关闭selinux

```
setenforce 0  ## 临时关闭

vi /etc/selinux/config
修改SELINUX=enforcing  为  SELINUX=disabled
SELINUX= can take one of these three values:
#     enforcing - SELinux security policy is enforced.
#     permissive - SELinux prints warnings instead of enforcing.
#     disabled - No SELinux policy is loaded.
```



3.配置主机名

```bash
## 设置主机名称命令
hostnamectl set-hostname k8s-master1
hostnamectl set-hostname k8s-node1
hostnamectl set-hostname k8s-node2

## 检测主机机名称
hostname
```

4.配置名称解析

```bash
## 编辑hosts

添加4行
vi /etc/hosts
192.168.244.132 k8s-master2
192.168.244.130 k8s-node1
192.168.244.131 k8s-node2
```



5.配置时间同步

```bash
选择一个节点作为服务端,其他作为时间节点做为客户端

master1为时间服务器的服务端
其他为时间服务的客户端

1)配置k8s-master1
# yum install chrony -y
# vi /etc/chrony.conf

修改三项:
server 127.127.1.0 iburst
allow 192.168.1.1/24
local stratum 10
## 启动时间服务端
systemtl start chronyd
##  重启
systemctl restart chronyd
## 自动启动
systemctl enable chronyd
检测端口启动:
ss -unl | grep 123
2) 配置k8s-node1 k8s-node2

vi  /etc/chrony.conf
修改服务
server k8s-master1 iburst

## 启动时间服务端
systemtl start chronyd
##  重启
systemctl restart chronyd
## 自动启动
systemctl enable chronyd
## 检测是否同步
chronyc sources
[root@node-192 ~]# chronyc sources
210 Number of sources = 1
MS Name/IP address         Stratum Poll Reach LastRx Last sample               
===============================================================================
^* k8s-master1                  10   6    77    36  -8020ns[ -122us] +/-  566us
```

6.关闭交换分区

```
临时关闭
swapoff -a

永久关闭swap
vi /etc/fstab

删除或者注释  文件中的该处内容
#/dev/mapper/centos-swap swap                    swap    defaults        0 0

检测是否已经关闭swap 命令
free -m
Swap:             0           0           0

```

## 三.给etcd颁发证书

1)创建证书颁发机构

2) 填写表单 --写明etcd 节点所在的IP

3)x向证书颁发机构申请证书

第一步.上传TLS.tar.gz 文件

第二步: 解压

  tar xvf TLS.tar.gz

cd TLS

执行 ./cfssl.sh

进入 cd etcd/

vim server-csr.json

   修改hosts中的IP地址

执行 ./generate_etcd_cert.sh

ls *.pem



自建CA

openssl

cfssl



安装cfssl

```bash
#下载cfssl
wget -c https://github.com/cloudflare/cfssl/releases/download/v1.5.0/cfssl_1.5.0_linux_amd64 -O /usr/local/bin/cfssl
# 下载cfssl-certinfo
wget -c https://github.com/cloudflare/cfssl/releases/download/v1.5.0/cfssl-certinfo_1.5.0_linux_amd64 -O /usr/local/bin/cfssl-certinfo

# 下载cfssljson
wget -c https://github.com/cloudflare/cfssl/releases/download/v1.5.0/cfssljson_1.5.0_linux_amd64 -O /usr/local/bin/cfssljson
# 赋予cfssl相关可执行权限
chmod a+x /usr/local/bin/cfssl*


```

颁发证书

（1）创建签证机构（CA）

定义一个ca-csr.json文件，此文件定义签证机构（CA）的相关信息，填入以下内容

```bash
{
    "CN": "etcd CA",
    "key": {
        "algo": "rsa",
        "size": 2048
    },
    "names": [
        {
            "C": "CN",
            "L": "Guizhou",
            "ST": "Qiandongnan"
        }
    ]
}
```

使用以下命令使用配置文件中，创建一个签证机构

```
cfssl gencert -initca ca-csr.json | cfssljson -bare ca -
```

会生成一个ca.pem公钥文件和ca-key.pem私钥文件，这两个文件是CA服务器的证书。接下来我们需要给客户端颁发证书。

（2）定义证书信息

先创建一个ca-config.json文件，定义待颁发证书的基本信息（如有效时间），填写以下内容

````
{
  "signing": {
    "default": {
      "expiry": "87600h"
    },
    "profiles": {
      "www": {
         "expiry": "87600h",
         "usages": [
            "signing",
            "key encipherment",
            "server auth",
            "client auth"
        ]
      }
    }
  }
}
````

再创建一个server-csr.json文件，定义待颁发证书的详细信息，如下

````
{
    "CN": "etcd",
    "hosts": [
        "192.168.244.133",
        "192.168.244.134",
        "192.168.244.135"
    ],
    "key": {
        "algo": "rsa",
        "size": 2048
    },
    "names": [{
        "C": "CN",
        "L": "Guizhou",
        "ST": "Qiandongnan"
    }]
}
cat > server-csr.json <<EOF
{
    "CN": "etcd",
    "hosts": [
    "192.168.244.130",
    "192.168.244.131",
    "192.168.244.132"
    ],
    "key": {
        "algo": "rsa",
        "size": 2048
    },
    "names": [
        {
            "C": "CN",
            "L": "BeiJing",
            "ST": "BeiJing"
        }
    ]
}
EOF
````

相关参数：

- CN：证书名称
- hosts：颁发的主机
- key：定义证书类型，algo为加密类型，size为加密长度
- names：证书的基本信息

（3）向签证机构（CA）申请证书

完成以上步骤，使用以下命令给客户端申请证书

```bash
cfssl gencert -ca=ca.pem -ca-key=ca-key.pem -config=ca-config.json -profile=www server-csr.json | cfssljson -bare server
```

此时，多出客户端两个证书相关文件server.pem和server-key.pem，使用`ls *pem`命令查看，可以看到四个pem文件，如下

```
ca-key.pem  ca.pem  server-key.pem  server.pem
```



## 四.部署etcd（）[教程](https://blog.51cto.com/u_14475593/2469610)



 etcd 需要三台主机

etcd 需要三台虚拟机 分别在 k8s-master1, k8s-node1 k8s-node2 上都安装etc	d

```bash
## 下载etcd
  https://github.com/coreos/etcd/
## ls
/opt/etcd/bin/etcdctl --ca-file=/opt/etcd/ssl/ca.pem --cert-file=/opt/etcd/ssl/server.pem --key-file=/opt/etcd/ssl/server-key.pem --endpoints="https://192.168.1.128:2379,https://192.168.1.127:2379,https://192.168.1.130:2379" cluster-health
```



下载地址：

wget -c https://github.com/etcd-io/etcd/releases/download/v3.2.32/etcd-v3.2.32-linux-amd64.tar.gz



```

wget -c https://github.com/etcd-io/etcd/releases/download/v3.3.10/etcd-v3.3.10-linux-amd64.tar.gz
```

**配置etcd二进制文件包**

```
//解压安装etcd
[root@master k8s]# tar zxf etcd-v3.3.10-linux-amd64.tar.gz

//建立存放etcd配置文件、命令、证书的目录
[root@master k8s]# mkdir -p /opt/etcd/{cfg,bin,ssl}
[root@master k8s]# ls /opt/etcd/
bin  cfg  ssl

//将etcd中的文件分门别类存放
[root@master k8s]# mv etcd-v3.3.10-linux-amd64/etcd /opt/etcd/bin/
[root@master k8s]# mv etcd-v3.3.10-linux-amd64/etcdctl /opt/etcd/bin/
[root@master k8s]# cp -p *.pem /opt/etcd/ssl/

//etcd命令文件
[root@master k8s]# ls /opt/etcd/bin/
etcd  etcdctl

//etcd证书
[root@master k8s]# ls /opt/etcd/ssl/
ca-key.pem  ca.pem  server-key.pem  server.pem
```



**建立etcd配置文件**

```
cat <<EOF >/opt/etcd/cfg/etcd
//[Member]
ETCD_DATA_DIR="/var/lib/etcd/default.etcd"
ETCD_LISTEN_CLIENT_URLS="https://192.168.244.133:2379"
ETCD_NAME="etcd01"

//[Clustering]
ETCD_LISTEN_PEER_URLS="https://192.168.244.133:2380"
ETCD_INITIAL_ADVERTISE_PEER_URLS="https://192.168.244.133:2380"
ETCD_ADVERTISE_CLIENT_URLS="https://192.168.244.133:2379"
ETCD_INITIAL_CLUSTER="etcd01=https://192.168.244.133:2380,etcd02=https://192.168.244.134:2380,etcd03=https://192.168.244.135:2380"
ETCD_INITIAL_CLUSTER_TOKEN="etcd-cluster"
ETCD_INITIAL_CLUSTER_STATE="new"
EOF
```

**建立etcd启动脚本**

```
cat <<EOF >/usr/lib/systemd/system/etcd.service
[Unit]
Description=Etcd Server
After=network.target
After=network-online.target
Wants=network-online.target

[Service]
Type=notify
EnvironmentFile=/opt/etcd/cfg/etcd
ExecStart=/opt/etcd/bin/etcd \
--name=etcd01 \
--data-dir=\${ETCD_DATA_DIR} \
--listen-peer-urls=\${ETCD_LISTEN_PEER_URLS} \
--listen-client-urls=\${ETCD_LISTEN_CLIENT_URLS},http://127.0.0.1:2379 \
--advertise-client-urls=\${ETCD_ADVERTISE_CLIENT_URLS} \
--initial-advertise-peer-urls=\${ETCD_INITIAL_ADVERTISE_PEER_URLS} \
--initial-cluster=\${ETCD_INITIAL_CLUSTER} \
--initial-cluster-token=\${ETCD_INITIAL_CLUSTER_TOKEN} \
--initial-cluster-state=new \
--cert-file=/opt/etcd/ssl/server.pem \
--key-file=/opt/etcd/ssl/server-key.pem \
--peer-cert-file=/opt/etcd/ssl/server.pem \
--peer-key-file=/op/ssl/server-key.pem \
--trusted-ca-file=/opt/etcd/ssl/ca.pem \
--peer-trusted-ca-file=/opt/etcd/ssl/ca.pem
Restart=on-failure
LimitNOFILE=65536

[Install]
WantedBy=multi-user.target
EOF
```

**推送证书、配置文件、启动脚本至node节点上**

```
//将证书、配置文件推送至其它节点
[root@master ~]# scp -r /opt/etcd/ssl/ root@192.168.244.134:/opt/ssl
[root@master ~]# scp -r /opt/etcd/ root@192.168.244.135:/opt/

//推送启动脚本
[root@master ~]# scp /usr/lib/systemd/system/etcd.service root@192.168.244.134:/usr/lib/systemd/system/
[root@master ~]# scp /usr/lib/systemd/system/etcd.service root@192.168.244.135:/usr/lib/systemd/system/
```

## node端方面

**更改各node节点收到的配置文件
node1**

````
    [root@node1 etcd]# vim /opt/etcd/cfg/etcd
//[Member]
ETCD_NAME="etcd02"                      //节点名称
ETCD_DATA_DIR="/var/lib/etcd/default.etcd"
ETCD_LISTEN_PEER_URLS="https://192.168.142.136:2380"               //节点地址
ETCD_LISTEN_CLIENT_URLS="https://192.168.142.136:2379"               //节点地址

//[Clustering]
ETCD_INITIAL_ADVERTISE_PEER_URLS="https://192.168.142.136:2380"               //节点地址
ETCD_ADVERTISE_CLIENT_URLS="https://192.168.142.136:2379"               //节点地址
````

**node 2**

```
 vim /opt/etcd/cfg/etcd
//[Member]
ETCD_NAME="etcd03"
ETCD_DATA_DIR="/var/lib/etcd/default.etcd"
ETCD_LISTEN_PEER_URLS="https://192.168.142.132:2380"                      //节点名称
ETCD_LISTEN_CLIENT_URLS="https://192.168.142.132:2379"                      //节点名称

//[Clustering]
ETCD_INITIAL_ADVERTISE_PEER_URLS="https://192.168.142.132:2380"                      //节点名称
ETCD_ADVERTISE_CLIENT_URLS="https://192.168.142.132:2379"   
```

## 所有节点开启服务

```
//各节点关防火墙
[root@node1 etcd]# systemctl stop firewalld.service
[root@node1 etcd]# setenforce 0
[root@node1 etcd]# systemctl disable firewalld.service

//开启etcd服务
[root@node1 etcd]# systemctl start etcd
[root@node1 etcd]# systemctl enable etcd
```

## 群集健康检查，查看群集成员

```
//群集各节点健康检查
[root@master ~]# /opt/etcd/bin/etcdctl --ca-file=/opt/etcd/ssl/ca.pem \
--cert-file=/opt/etcd/ssl/server.pem --key-file=/opt/etcd/ssl/server-key.pem \
--endpoints="https://192.168.244.133:2379,https://192.168.244.134:2379,https://192.168.244.135:2379" \
cluster-health

//查看etcd群集成员
[root@master ~]# /opt/etcd/bin/etcdctl --ca-file=/opt/etcd/ssl/ca.pem \
--cert-file=/opt/etcd/ssl/server.pem --key-file=/opt/etcd/ssl/server-key.pem \
--endpoints=="https://192.168.142.132:2379,https://192.168.142.131:2379,https://192.168.142.130:2379"  member list
```