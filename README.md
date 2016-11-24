# docker-cheatsheet
cheat sheet for docker

## Docker基础概念

### 1.镜像(image)

镜像是面向`Docker引擎`的、能够为应用提供`创建运行时环境`的`只读`模板，
主要包括`文件系统`和一些`参数`。

官方文档解释：An image is a `filesystem` and `parameters` to use at runtime. It doesn’t have state and `never changes`.

镜像为应用运行时环境的创建和分享，提供了一种便捷的手段。

### 2.容器(container)

容器是从镜像创建的`运行时实例`，可以将其启动、开始、停止、删除，容器之间相互隔离、互不可见。

镜像是只读的，但在容器从镜像启动的时候，Docker会在镜像的最上层创建一个`可写层`，镜像本身仍将保持不变。
就像用ISO装系统之后，ISO文件本身并没有什么变化一样。

### 3.仓库(repository)

像Github这样的代码仓库是用来保存代码的，Docker仓库则是用来`集中存放镜像`的。

Docker仓库保存在`注册服务器(registry)`上。

一个注册服务器上可保存多个镜像仓库，通常一个镜像仓库保存某一类的多个镜像，
多个镜像用不同的`标签(tag)`来区分。

例如：存放Ubuntu操作系统镜像的仓库，称为Ubuntu仓库，其中可能包括14.04,12.04等不同版本的镜像。



根据存储的镜像公开分享与否，Docker仓库分为如下两种：

* 公开仓库(Public)
  * 最大的公开仓库是Docker Hub
  * 国内的公开仓库包括Docker Pool等，可以提供稳定的国内访问
* 私有仓库(Private)：
  * Docker也支持用户在本地网络内创建一个只能自己访问的私有仓库。

## 镜像常用操作

* 从仓库获取镜像

```
docker pull 镜像名称:镜像标签
```

如：docker pull docker/whalesay、docker pull centos:latest

* 查看镜像

```
docker images
docker images -a 或 docker images --all（列出全部）
docker images -q 或 docker images --quiet（只显示数字格式的IMAGE ID）
当然也可以混合参数使用：docker iamges -qa
docker images --tree 查看所有层
```
 
* 创建镜像

```
方法一：通过Dockerfile创建镜像
docker build <Dockerfile所在路径 或 URL 或 -(即从标准输入读入)>
例如：docker build -t docker-whale . (其中，-t 用于设置仓库名和标签名)
方法二：基于已有镜像创建
docker commit
方法三：从镜像导出的文件恢复
docker import
```

* 搜索镜像
```
docker search 镜像名称
```

删除镜像
```
docker rmi 镜像名称或镜像ID
docker rmi -f 镜像名称或镜像ID
```

* 启动镜像
```
docker run 镜像名称
```
 
## 容器常用操作
* 查看容器
```
docker ps -a
docker ps -l 只查看最后一个容器 
```

```
docker login

docker push

 

打印容器的标准输出

docker logs

docker stop

docker version

docker run -P 容器内外使用相同的端口

docker run -i 交互式执行

docker run -t 分配一个伪终端

docker run -p port_host:port_container

docke run --name web 指定容器名

docker run -v /container-dir 添加一个data volume（容器内的路径/inside_path）

docker run -v host-dir:/container-dir 以mount方式添加一个data volume container-dir必须绝对路径


docker run -v host-dir:/container-dir:ro 只读方式挂载data volume


docker run --rm -it -v ~/.bash_history:/root/.bash_history ubuntu /bin/bash 只挂在一个文件而非目录

docker run --rm -v /foo -v awesome:/bar busybox top 默认情况下data volume是持久的，删除容器也不会删掉data volume，这里通过--rm指定了data volum在删除容器时也跟随着一起被删除

查看映射到宿主机的端口

docker port CONTAINER_NAME | CONTAINER_ID  CONTAINER_PORT

查看标准输出(-f类似于tail -f)

docker logs -f CONTAINER_NAME

docker top CONTAINER_NAME 查看进程

docker inspect CONTAINER_NAME 查看容器配置和状态信息(JSON格式)

docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' CONTAINER_NAME 


docker inspect --format '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' CONTAINER_NAME 


docker start CONTAINER_NAME

docker rm CONTAINER_NAME
```

## 在容器中执行命令

```
docker attach attach到一个已经运行的容器的stdin，然后进行命令执行的动作。 
但是需要注意的是:如果从这个stdin中exit，会导致容器的停止

docker exec -i -t CONTAINER_NAME CMD

docker exec -d CONTAINER_NAME CMD 在后台执行CMD
```
## 持久化

* 镜像持久化
```
docker images
docker save image-name > /home/image-saved.tar
docker load < /home/image-saved.tar 
```

* 容器持久化
```
docker ps -a
docker export <CONTAINTER_ID> /home/container-exported.tar
cat /home/container-exported.tar | sudo docker import - new-container-name:version
```

注意：
通过`docker image --tree`可查看出区别：

导出后再导入(exported-imported)的镜像会丢失所有的历史，
而保存后再加载（saveed-loaded）的镜像没有丢失历史和层(layer)。

这意味着使用导出后再导入的方式，你将无法回滚到之前的层(layer)，
同时，使用保存后再加载的方式持久化整个镜像，就可以做到层回滚
（可以执行`docker tag <LAYER ID> <IMAGE NAME>`来回滚之前的层）。

## 参考链接
* [Docker 镜像、容器、仓库的概念](http://blog.csdn.net/smalosnail/article/details/53117496)
* [Get started with Docker](https://docs.docker.com/engine/getstarted/)
* [Learn by example](https://docs.docker.com/engine/tutorials/)
* [Docker的save和export命令的区别](https://my.oschina.net/zjzhai/blog/225112)
