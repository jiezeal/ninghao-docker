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
docker ps
```

###服务的生命周期
```
// 查看应用的服务
docker-compose ps
// 停止phoenix服务
docker-compose stop phoenix
// 停止所有在docker-compose.yml文件中定义的服务
docker-compose stop
// 重新启动phoenix服务
docker-compose start phoenix
// 启动所有服务
docker-compose start	
// 查看服务的日志
docker-compose logs
// 持续跟踪服务日志的变化
docker-compose logs -f
// 登录到phoenix服务容器中
docker-compose exec phoenix bash
// 要删除应用的服务需要先把它们都停止掉
docker-compose stop
// 删除所有服务的容器
```
