### 1. 安装mysql8.0

 #### 卸载旧版mysql 

[使用教程](https://www.wandouip.com/t5i431300/)

#### 安装新本mysql8.0

[安装教程](https://developer.aliyun.com/article/770498?spm=a2c6h.12873581.0.0.655851beRxLxCW&groupCode=database)

#### 启动失败解决

[解决方法](https://segmentfault.com/a/1190000012289076)

#### 数据库操作

[操作命令](https://www.cnblogs.com/zipxzf/p/10718544.html)

#### 修改密码:

```mysql
ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'newpassword'; #更新一下用户的密码 root用户密码为newpassword

ALTER USER 'root'@'localhost' IDENTIFIED BY 'new password';
```

#### 刷新数据库

```mysql
mysql刷新权限命令：（一般用于数据库用户信息更新后）
FLUSH PRIVILEGES;
```

#### 打开3306端口

```bash
firewall-cmd --add-port=3306/tcp --permanent
```



mysql  密码

192.168.31.130:3306

root

密码:d&eXwM7aIwgpcdNA2Xv&BjX8Ku$8LSA5

192.168.31.134:3306

root

密码:E^c^pA#&$g$HWua5#e$11xUZ%BHZYthE

从库复制密码的账号:

repl

密码:yh19h#Z%W7hM^09NXjk#8u1&^UW!JDRb

myslave

密码:yh19h#Z%W7hM^09NXjk#8u1&^UW!JDRb

执行语句:

```mysql
CHANGE MASTER TO MASTER_HOST='192.168.31.134', MASTER_USER='myslave1', MASTER_PASSWORD='yh19h#Z%W7hM^09NXjk#8u1&^UW!JDRb', MASTER_LOG_FILE='mysql-bin.000004', MASTER_LOG_POS=4329;


// grant all privileges on *.* to ‘username’@’ip’ identified by ‘password’ with grant option;
// flush privileges;


// 创建从库访问主库的账户
//grant all privileges on *.* to ‘username’@’ip’ identified by ‘password’ with grant option;
// 创建用户 
  //create user 'zabbix'@'localhost' IDENTIFIED BY '111111';
  create user 'myslave'@'192.168.31.130' IDENTIFIED BY 'yh19h#Z%W7hM^09NXjk#8u1&^UW!JDRb';
  // CREATE USER myslave1@'192.168.31.130' IDENTIFIED BY 'yh19h#Z%W7hM^09NXjk#8u1&^UW!JDRb';
  赋予权限:
  GRANT REPLICATION SLAVE ON *.* TO  'myslave1'@'192.168.31.130';
  // GRANT file ON *.* TO 'myslave1'@'192.168.31.130' IDENTIFIED BY 'yh19h#Z%W7hM^09NXjk#8u1&^UW!JDRb';
  grant all privileges on *.* to 'myslave'@'192.168.31.130'
  
  grant all privileges on *.* to 'myslave'@'192.168.31.130' identified by '4lhkD4kWbPTY4LhwdGwgNbA3PWmJYkX5' with grant option;
flush privileges;

// 
change master to master_host=’ip’, master_user=’username’, master_password=’password’, master_log_file=’log_file_name’;


修改密码:如果远程连接的时候报plugin caching_sha2_password could not be loaded这个错误，可以尝
alter user 'myslave'@'192.168.31.130' identified with  mysql_native_password by 'yh19h#Z%W7hM^09NXjk#8u1&^UW!JDRb';
```





#### mycat 安装

下载地址:http://dl.mycat.org.cn/



#### centos 端口查询

https://www.cnblogs.com/heqiuyong/p/10460150.html

远程连接其他数据库:

**在本地远程连接**

在终端输入：

```
mysql -h 服务器ip地址 -P 端口 -u 用户名 -p

// mysql -h 192.168.31.134 -u 3306 root -p
```



