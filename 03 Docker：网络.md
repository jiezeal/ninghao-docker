#Docker：网络

###网络
```
// 查看已有的网络
docker network ls
// 查看bridge网络的底层信息
docker network inspect bridge
// 创建一个后台运行的容器web1 --net用来指定要使用的网络，如果不加会默认使用bridge网络
// 如果本机没有nginx镜像，系统会自动从网络上下载
docker run -d --name web1 --net bridge nginx
// 再次查看bridge网络的底层信息
docker network inspect bridge
// 再创建一个容器web2
docker run -d --name web2 --net bridge nginx
// 再次查看bridge网络的底层信息，可以查看到已经有两个容器使用bridge网络了，在这个网络下面的容器之间可以相互通信
docker network inspect bridge
// 登录到web1
docker exec -it web1 bash
ping 172.17.0.3
// 可以ping通，说明web1和web2之间可以相互通信
```

###理解none网络
```
// 创建一个容器并指定none网络
docker run -d --name web_none --net none nginx
// 查看none网络的底层信息，可以看到web_none容器在使用none网络，但是并没有一个可以使用的ip地址，也就是说这个容器跟外界是一个完全隔离的状态，谁也没有办法和它通信
docker network inspect none
```
![](image/screenshot_1485704053236.png)

###理解host网络
```
// 创建一个容器并指定host网络
docker run -d --name web_host --net host nginx
// 查看host网络的底层信息，可以看到web_host容器在使用host网络，但是也没有一个可以使用的ip地址，因为使用host网络的容器的ip地址跟主机的网络是一样的
docker network inspect host
```

###Docker Quickstart Terminal启动蓝屏解决办法
BIOS开启虚拟化
![](image/screenshot_1485783059483.png)

###端口
```
// 如果想让外界可以访问到默认的birdge网络上的容器提供的服务，需要告诉docker要使用哪一些端口
// 查看镜像使用的端口
docker inspect nginx
// 在创建容器的时候，我们可以去指定一下主机跟容器之间的端口映射关系 
// 80（主机的80端口）:80（容器的80端口） 如果有人访问主机上面的80端口的时候，这个访问会被定向到这个容器上面的80端口
docker run -d --name web3 --publish 80:80 nginx
// 查看主机的ip地址
docker-machine inspect default
// 使用浏览器访问这个ip地址，显示的内容就是web3这个容器的nginx提供的服务
```

###端口绑定
```
// 查看容器跟主机绑定的端口
docker inspect web3
// 或者使用（显示结果左边是容器端口，右边是主机端口）
docker port web3			
// 删除web3容器
docker rm web3
// 重新创建一个容器，让它的80端口指向主机的一个随机端口， 这次在浏览器访问需要用 http://192.168.99.100:32768/去访问
docker run -d --name web3 --publish 80 nginx
// 删除web3容器
docker rm -f web3
// 重新创建一个容器，使用--publish-all 或者 -P 选项，使用它我们可以不需要去手工指定端口或者是端口的一个映射关系，它会自动去公布在镜像里面设置的要公布的所有的端口
docker run -d --name web3 --publish-all nginx
docker port web3
// 在浏览器中访问需要使用 http://192.168.99.100:32770/ 去访问
```

###自定义网络
我们可以基于某一个类型的网络去创建一些自定义的网络，这样属于这个网络的容器就会被单独的隔离出来，它们之间可以进行相互通信，但是其它不在这些自定义网络上面的容器不能够直接访问到它们，一个容器可以属于多个网络，在用户自定义网络上面的容器，它们直接可以使用各自容器的名字来访问到对方，因为它们会用到docker内嵌的dns服务
```
// 创建一个新的网络
docker network create --driver bridge web
// 查看web网络的信息
docker network inspect web
```

###把容器放到自定义网络里
```
// 创建容器并指定网络为自定义的web网络
docker run -d --name web5 --net web nginx
// 查看web网络
docker network inspect web
// 将已经存在的容器放到指定的网络里面
docker network connect web web3
docker network inspect web
// 登录到web3
docker exec -it web3 bash
ping web5
// 通过ip addr我们可以发现web3现在属于两个网络，一个是bridge网络，一个是web网络
ip addr
// 将web3容器从bridge网络中删除
docker network disconnect bridge web3
docker network inspect bridge
```