#Docker：容器化应用

###定义数据库服务
```
version: '2'
services:
  db:
    image: mariadb:10.1
    environment:
      MYSQL_ROOT_PASSWORD: "root"
      MYSQL_DATABASE: "app"
      MYSQL_USER: "app"
      MYSQL_PASSWORD: "123123"
    volumes:
      - db:/var/lib/mysql
volumes:
  db:
    driver: local
```

###测试数据库服务
```
docker-compose up -d
docker-compose exec db bash
mysql -u root -p
show databases;
create database test default charset utf8;
show databases;
exit
exit
docker-compose down
docker-compose up -d
docker-compose exec db bash
mysql -u root -p
show databases;
```

###定义PHP服务
```
version: '2'
services:
  php:
    image: php:7.0-fpm
    volumes:
      - ./app:/mnt/app
  db:
    image: mariadb:10.1
    environment:
      MYSQL_ROOT_PASSWORD: "root"
      MYSQL_DATABASE: "app"
      MYSQL_USER: "app"
      MYSQL_PASSWORD: "123123"
    volumes:
      - db:/var/lib/mysql
volumes:
  db:
    driver: local
```

phpinfo.php
```
<?php phpinfo(); ?>
```


