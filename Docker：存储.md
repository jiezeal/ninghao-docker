#Docker：存储

存储
```
// 查看镜像或容器的底层信息
docker inspect centos
```
```
docker run -i -t --name test1 centos bash
echo 'hello www.baidu.com' > hello.txt
cat hello.txt	// 会输出文件里面的内容
exit
```
```
docker run -i -t --name test2 centos bash
cat hello.txt	// 会提示 cat: hello.txt: No such file or directory
```

`虽然test1还有test2都是基于centos这个镜像创建的，不过它们都拥有一个各自的可以读写的一个文件层，新创建的文件或者被修改的已有的文件都会被放到这个文件层里面，不会影响到镜像本身或者其它的使用这个镜像创建的容器`