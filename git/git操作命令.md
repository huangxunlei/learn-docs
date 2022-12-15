## 1、修改远程仓库地址

```
```

## 2、Linux 升级git到最新版本

我安装的是[CentOS](https://www.linuxprobe.com/)-7-x86_64-1908，自带的git是1.8.3.1，在终端下输入以下[命令](https://www.linuxcool.com/)查看

```
git --version
```

如果当前已经安装了其他版本的git，并且是通过安装包安装在了默认路径，那么最好先卸载掉旧版本的git，否则安装新版本的git后，在调用git时，会出现交叉调用的情况。如果旧版本是编译安装，并且指定了安装路径，那么可以不用卸载，但是在安装完新版本的git后一定配置好环境变量。

**1、卸载旧版本的git**

在终端输入

```bash
yum remove git 
```

**2、安装依赖软件**

在终端输入

```bash
yum install curl-devel expat-devel gettext-devel openssl-devel zlib-devel asciidoc
yum install gcc perl-ExtUtils-MakeMaker
```

你可能并不需要安装依赖就能make通过，我的环境安装了第一个

**3、下载最新Git源码**

打开网址https://github.com/git/git/releases，可以在这里查看所有git的发布版本，复制你想要下载的源码下载地址，在终端输入：

我之前安装的git 还没有卸载，所以我是直接git clone 最新版本进行编译安装的

**4、编译安装最新的git版本**

取用官网的命令如下

```bash
git clone https://github.com/git/git
## 等待下载完成之后
# 此时可以卸载以前默认安装的git  命令 yum remove git
cd git 
make prefix=/usr/local/git all
make make prefix=/usr/local/git install
```

**5、添加环境变量**

在终端输入

```bash
echo "export PATH=$PATH:/usr/local/git/bin" >>/etc/profile

source /etc/profile
```

**6、验证版本**

在终端输入

```bash
git --version
```

**7、如果你的git访问https链接时会报错：SSL connect error。更新nss**

在终端输入

```bash
yum update nss
```

