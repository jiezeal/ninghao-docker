#Docker：组合

###定义服务
vi docker-compose.yml
```
version: '2'
services:
  phoenix:
    image: nginx
    ports:
      - "8080:80"
  dragon:
    image: nginx
    ports:
      - "8081:80"
```

###启动服务
```
// 启动服务
docker-compose up
// 通过浏览器访问 http://192.168.99.100:8080/		http://192.168.99.100:8081/
// 回到终端，这里会显示容器里面的一些日志，每条日志的前面会标注一下这个日志来自哪一个服务容器，ctrl+c可以停止它们，这些服务我们可以让它在后台去运行
docker-compose up -d
// 查看一下正在运行的容器
```
