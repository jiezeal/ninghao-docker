#Docker：镜像与容器

###下载 Docker Toolbox
[https://www.docker.com/products/docker-toolbox](https://www.docker.com/products/docker-toolbox)  

###下面这个网站有一些别人做好的镜像
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
// 重启容器
docker restart greeting
// 启动容器
docker start greeting
// 查看容器日志
docker logs greeting
// 创建一个带互动的容器
docker run --interactive --tty centos /bin/bash
// 登录到default主机
docker-machine ssh default
```

###在后台运行的容器
```
// 创建一个在后台运行的容器
docker run --detach centos ping www.baidu.com
// 新打开一个终端
docker logs --follow e183ced42544
// 切换到原来的终端
docker stop e183ced42544
// 再切换到新打开的终端就会发现日志已经停止打印
```

###手工创建镜像
```
// 先创建一个容器
docker run -i -t centos bash
// 再添加一个nodejs的安装源
curl --silent --location https://rpm.nodesource.com/setup_6.x | bash -
// 再用yum去安装一下nodejs
yum install nodejs -y
// 用nodejs在终端输出hello
node -e "console.log('hello')"
// 下面我们可以基于这个容器创建一个镜像，其实就是去提交一下对这个容器的修改就行了
// 先复制一个这个容器的主机名，因为它是这个容器的ID号，输入exit退出一下
// 提交修改 -m 指定提交日志(中间不能有空格) -a 指定作者 
commit -m '安装nodejs' -a 'zhulinjie' 79944f6655f7 zhulinjie/nodejs-demo:latest
// 基于这个镜像去创建一个容器
docker run zhulinjie/nodejs-demo node -e "console.log('hello')"
// 删除手工创建的镜像，需要先删除基于这个镜像创建的容器
docker ps -a -l
docker rm 8cb93622ed06
docker rmi zhulinjie/nodejs-demo
```

###Dockerfile创建镜像
```
cd Desktop
mkdir nodejs-demo
cd nodejs-demo
vi Dockerfile
```
```
FROM centos
MAINTAINER zhulinjie <zhulinjie@126.com>
RUN curl --silent --location https://rpm.nodesource.com/setup_6.x | bash -
RUN yum install nodejs -y
```
```
docker build --tag zhulinjie/nodejs-demo:latest .
docker images
```