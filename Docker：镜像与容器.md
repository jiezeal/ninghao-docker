#Docker：镜像与容器

下载 Docker Toolbox
[https://www.docker.com/products/docker-toolbox](https://www.docker.com/products/docker-toolbox)

搜索镜像
[https://hub.docker.com/explore/](https://hub.docker.com/explore/)
```
docker search ubuntu
```

下载镜像
```
// 搜索镜像
docker search ubuntu
// 查看在本地已有的镜像
docker images							
// 下载镜像
docker pull ubuntu			
// 再查看一下本地已有镜像
docker images
```

###Docker常用命令
```
// 查看本地已安装的机器
docker-machine ls
// 删除一台机器
docker-machine rm default
```

