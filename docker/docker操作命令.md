## 清理镜像

我们在使用 Docker 一段时间后，系统一般都会残存一些临时的、没有被使用的镜像文件，可以通过以下命令进行清理：

```bash
docker image prune
docker image prune -a -f
```

它支持的子命令有：

- `-a, --all`: 删除所有没有用的镜像，而不仅仅是临时文件；
- `-f, --force`：强制删除镜像文件，无需弹出提示确认；

另外，执行完 `docker image prune` 命令后，还是告诉我们释放了多少存储空间！

## 删除所有未运行的容器（已经运行的删除不了，未运行的就一起被删除了）

```bash
 docker rm $(sudo docker ps -a -q)
```

## 删除空镜像

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