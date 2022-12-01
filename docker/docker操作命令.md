

## docker打镜像

 ### jar包打镜像

Dockerfile

```bash
FROM openjdk:8-jdk-alpine
# 将jar包添加到容器
ADD *.jar  app.jar
# 定义容器启动执行的命令
# CMD java -Xmx1024m -jar app.jar --spring.profiles.active=prod
ENTRYPOINT ["java","-Xmx1024m","-Xmx1024m","-jar","app.jar","--spring.profiles.active=prod","-c"]
```

### 制作镜像命令

```bash
# 记住后面有顿号
docker build -f Dockerfile -t 镜像名称:版本号 .
```



## 1、docker复制



### 1.1 从容器内复制到宿主机

```bash
docker cp NAME:/path  /path
docker cp 6689deaa608d:/data/YH/logs/demo/2021-08-02/51965.log /opt
```

### 1.2 从宿主机拷贝到容器中

```ba
docker cp  /path  NAME:/path  
docker cp /opt/test.js testtomcat：/usr/local/tomcat/webapps/test/js
```

### 1.3 docker  挂载

```bash
docker run -d  -v /data:/data image_id
```

### 1.4 暴露端口号

````bash
docker run -d  -p 80:80 image_id
````

查看最近30分钟的日志:

```shell
docker logs --since 30m CONTAINER_ID
```



k8s 批量删除  移出的pod

```bash
kubectl delete pods `kubectl get pods -n prod|grep Evicted |awk '{print $1}'` -n prod
```

```bash
docker run -d -p 8888:8888 -v /RHZYDATA/xmc-B-logs:/data/logs registry.cn-shenzhen.aliyuncs.com/peopledata/xxxxx:c7292ca

docker run -d registry.cn-shenzhen.aliyuncs.com/peopledata/xxxx:5500a487
```

通过docker logs命令可以查看容器的日志。

**命令格式：**



```shell
$ docker logs [OPTIONS] CONTAINER
  Options:
        --details        显示更多的信息
    -f, --follow         跟踪实时日志
        --since string   显示自某个timestamp之后的日志，或相对时间，如42m（即42分钟）
        --tail string    从日志末尾显示多少行日志， 默认是all
    -t, --timestamps     显示时间戳
        --until string   显示自某个timestamp之前的日志，或相对时间，如42m（即42分钟）
```

**例子：**

查看指定时间后的日志，只显示最后100行：



```shell
$ docker logs -f -t --since="2018-02-08" --tail=100 CONTAINER_ID
```

查看最近30分钟的日志:



```shell
$ docker logs --since 30m CONTAINER_ID
$ docker logs --since 30m CONTAINER_ID
```

查看某时间之后的日志：



```shell
$ docker logs -t --since="2018-02-08T13:23:37" CONTAINER_ID
```

查看某时间段日志：



```shell
$ docker logs -t --since="2018-02-08T13:23:37" --until="2018-02-09T12:23:37" CONTAINER_ID
```

参考:
 [https://segmentfault.com/a/1190000010086763](https://link.jianshu.com?t=https%3A%2F%2Fsegmentfault.com%2Fa%2F1190000010086763)







## 2、清理镜像

我们在使用 Docker 一段时间后，系统一般都会残存一些临时的、没有被使用的镜像文件，可以通过以下命令进行清理：

```bash
docker image prune
docker image prune -a -f
```

它支持的子命令有：

- `-a, --all`: 删除所有没有用的镜像，而不仅仅是临时文件；
- `-f, --force`：强制删除镜像文件，无需弹出提示确认；

另外，执行完 `docker image prune` 命令后，还是告诉我们释放了多少存储空间！

## 3、删除所有未运行的容器（已经运行的删除不了，未运行的就一起被删除了）

```bash
 docker rm $(sudo docker ps -a -q)
```

## 4、删除空镜像

删除none的镜像，要先删除镜像中的容器。要删除镜像中的容器，必须先停止容器。

```bash
$ docker images

$ docker rmi $(docker images | grep "none" | awk '{print $3}') 
直接删除带none的镜像，直接报错了。提示先停止容器。

$ docker stop $(docker ps -a | grep "Exited" | awk '{print $1 }') //停止容器

$ docker rm $(docker ps -a | grep "Exited" | awk '{print $1 }') //删除容器

$ docker rmi $(docker images | grep "none" | awk '{print $3}') //删 除镜像
```


      docker 'docker tag  docker images|grep air |awk \'{print $3}\'  registry.cn-shenzhen.aliyuncs.com/xinkong-project/my-blog:latest'
      docker  'docker push registry.cn-shenzhen.aliyuncs.com/xinkong-project/my-blog:latest'