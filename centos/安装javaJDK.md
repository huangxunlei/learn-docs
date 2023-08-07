# 1、下载java版本到本地

java下载版本地址：http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html

一般下载64位版本，这里选择的版本是java版本1.8

![img](https://img2018.cnblogs.com/blog/1479758/201906/1479758-20190620104526200-25168274.png)

rpm版本：下载后可以直接用yum install命令安装或者rpm命令安装

tar.gz版本：下载到本地后解压安装

下载tar.gz版本

# 2、安装

（1）创建安装目录

```bash
mkdir /usr/local/java/
```

  (2)  解压到新的目录

```bash
tar -zxvf jdk-8u271-linux-x64.tar.gz - c /usr/local/java/
```

# 3、设置环境变量

打开文件

```bash
vim /etc/profile
```

在文件末尾添加

```bash
export JAVA_HOME=/usr/local/java/jdk1.8.0_301
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
export PATH=${JAVA_HOME}/bin:$PATH
```

使环境变量生效

```bash
source /etc/profile
```

添加软连接

```bash\
ln -s /usr/local/java/jdk1.8.0_371/bin/java /usr/bin/java
```

检查

```bash
java -version
```

```bash
java version "1.8.0_271"
Java(TM) SE Runtime Environment (build 1.8.0_271-b09)
Java HotSpot(TM) 64-Bit Server VM (build 25.271-b09, mixed mode)
```

