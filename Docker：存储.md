#Docker：存储

存储
```
// 查看镜像或容器的底层信息
docker inspect centos
```
```
docker run -i -t --name test1 centos bash
echo 'hello www.baidu.com' > hello.txt
cat hello.txt
exit
```