#Docker：网络

###网络
```
// 查看已有的网络
docker network ls
// 查看bridge网络的底层信息
docker network inspect bridge
// 创建一个后台运行的容器 --net用来指定要使用的网络，如果不加会默认使用bridge网络
docker run -d --name web1 --net bridge nginx
```