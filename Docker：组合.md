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

