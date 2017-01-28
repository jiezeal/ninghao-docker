#Docker：镜像与容器

下载 Docker Toolbox
[https://www.docker.com/products/docker-toolbox](https://www.docker.com/products/docker-toolbox)  

下面这个网站有一些别人做好的镜像
[https://hub.docker.com/explore/](https://hub.docker.com/explore/)

###Docker常用命令
```
// 搜索镜像
docker search centos
// 查看在本地已有的镜像
docker images							
// 下载镜像
docker pull centos
// 查看本地已安装的机器
docker-machine ls
// 删除一台机器
docker-machine rm default
// 创建容器 （系统会给这个容器分配一个默认的名字）
docker run centos /bin/echo 'hello'
// 查看正在运行的容器
docker ps
// 查看所有容器
docker ps --all
// 可以基于一个镜像创建多个容器
docker run centos ls
// 查看所有容器（简写）
docker ps -a
// 删除容器
docker rm 4507ade88ba8
// 也可以在创建容器的时候给容器取一个名字
docker run --name greeting centos /bin/echo 'hello'
// 查看最近一次创建的容器
docker ps --all --latest
// 停止容器
docker stop greeting
// 重启窗口
docker restart greeting
// 启动容器
docker start greeting
// 查看容器日志
docker logs greeting
// 创建一个带互动的容器
docker run --interactive --tty centos /bin/bash
```

在后台运行的容器
```
// 创建一个在后台运行的容器
docker run --detach centos ping www.baidu.com
// 
```






