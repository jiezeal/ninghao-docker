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

./app/phpinfo.php
```
<?php phpinfo(); ?>
```

###定义nginx服务
```
version: '2'
services:
  web:
    image: nginx:1.11.1
    ports:
      - "8080:80"
    depends_on:
      - php
    volumes_from:
      - php
    volumes:
      - ./images/nginx/config:/etc/nginx/conf.d
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

./images/nginx/config/default.conf
```
server {
	listen				80;
	server_name			localhost;
	root				/mnt/app;
	index				index.php index.html index.htm;

	location / {
		index			index.php index.html index.htm;
		try_files		$uri $uri/ /index.php?$query_string;
	}

	location ~ \.php$ {
		fastcgi_pass	php:9000;
		fastcgi_index	index.php;
		fastcgi_param	SCRIPT_FILENAME		$document_root$fastcgi_script_name;
		include			fastcgi_params;
	}
}
```

./.env
```
COMPOSE_CONVERT_WINDOWS_PATHS=1
```
![](image/screenshot_1485923966998.png)

测试
```
docker-compose up -d
```

通过浏览器访问：http://192.168.99.100:8080/phpinfo.php
![](image/screenshot_1485923916768.png)

###创建自己的服务
现在我们在compose文件里面定义的服务用的都是现成的镜像，有时候我们需要去定制一下这些镜像。比如说去添加一下自己的配置，去安装新的模块等等。比如我想去修改一下PHP服务里面的一些配置，现在你可以看到它的 upload_max_filesize 它的值是2M
```
version: '2'
services:
  web:
    image: nginx:1.11.1
    ports:
      - "8080:80"
    depends_on:
      - php
    volumes_from:
      - php
    volumes:
      - ./images/nginx/config:/etc/nginx/conf.d
  php:
    build:
      context: ./images/php
      dockerfile: Dockerfile
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

./images/php/Dockerfile
```
FROM php:7.0-fpm
MAINTAINER zhulinjie <zhulinjie_cool@126.com>

COPY ./config/php.ini /usr/local/etc/php/conf.d
```

./images/php/config/php.ini
```
memory_limit = 256M
post_max_size = 100M
upload_max_filesize = 100M
```

```
// 创建服务需要的镜像
docker-compose build
// 重新创建需要的服务
docker-compose up -d
```
通过浏览器访问：http://192.168.99.100:8080/phpinfo.php upload_max_filesize的值已经变为了100M

###安装PHP模块
./images/php/Dockerfile
```
FROM php:7.0-fpm
MAINTAINER zhulinjie <zhulinjie_cool@126.com>

RUN apt-get update && apt-get install -y libpng12-dev libjpeg-dev && rm -rf /var/lib/apt/lists/* && docker-php-ext-configure gd --with-png-dir=/usr --with-jpeg-dir=/usr && docker-php-ext-install gd pdo_mysql zip opcache

COPY ./config/php.ini /usr/local/etc/php/conf.d
COPY ./config/opcache-recommended.ini /usr/local/etc/php/conf.d
```

./images/php/config/opcache-recommended.ini
```
opcache.memory_consumption=128
opcache.interned_strings_buffer=8
opcache.max_accelerated_files=4000
opcache.revalidate_freq=60
opcache.fast_shutdown=1
opcache.enable_cli=1
```

```
// 创建服务需要的镜像
docker-compose build
// 重新创建需要的服务
docker-compose up -d
```

通过浏览器访问：http://192.168.99.100:8080/phpinfo.php 搜索 gd pdo opcache

###创建工具包容器
```
version: '2'
services:
  console:
    build:
      context: ./images/console
      dockerfile: Dockerfile
    volumes_from:
      - php
    tty: true
  web:
    image: nginx:1.11.1
    ports:
      - "8080:80"
    depends_on:
      - php
    volumes_from:
      - php
    volumes:
      - ./images/nginx/config:/etc/nginx/conf.d	
  php:
    build:
      context: ./images/php
      dockerfile: Dockerfile
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

./images/console/Dockerfile
```
FROM php:7.0
MAINTAINER zhulinjie <zhulinjie_cool@126.com>
```

###安装配置git composer
./images/console/Dockerfile
```
FROM php:7.0
MAINTAINER zhulinjie <zhulinjie_cool@126.com>

RUN apt-get update && apt-get install -y git curl vim libfreetype6-dev \
	&& rm -rf /var/lib/apt/lists/* \
	&& docker-php-ext-install pdo_mysql zip

RUN git config --global user.name "zhulinjie" \
	&& git config --global user.email "zhulinjie_cool@126.com"

RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
	&& php -r "if (hash_file('SHA384', 'composer-setup.php') === '55d6ead61b29c7bdee5cccfb50076874187bd9f21f65d8991d46ec5cc90518f447387fb9f76ebae1fbbacf329e583e30') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;" \
	&& php composer-setup.php \
	&& php -r "unlink('composer-setup.php');" \
	&& mv composer.phar /usr/local/bin/composer \
	&& echo 'export PATH="$PATH:$HOME/.composer/vendor/bin"' >> ~/.bashrc \
	&& . ~/.bashrc && composer config -g repo.packagist composer https://packagist.phpcomposer.com

RUN composer global require "laravel/installer"
```

```
docker-compose build console
```

![](image/screenshot_1485962575074.png)

###安装运行 Laravel 项目
```
docker-compose up -d
docker-compose exec console bash
composer create-project --prefer-dist laravel/laravel laravel
ls
exit
```

./app/laravel/.env
```
APP_ENV=local
APP_KEY=base64:bYkBJeE4ybRDWCGeBaj2cN4RPy2XvZp5PO06IC4uLzI=
APP_DEBUG=true
APP_LOG_LEVEL=debug
APP_URL=192.168.99.100:8080

DB_CONNECTION=mysql
DB_HOST=db
DB_PORT=3306
DB_DATABASE=app
DB_USERNAME=app
DB_PASSWORD=123123

BROADCAST_DRIVER=log
CACHE_DRIVER=file
SESSION_DRIVER=file
QUEUE_DRIVER=sync

REDIS_HOST=127.0.0.1
REDIS_PASSWORD=null
REDIS_PORT=6379

MAIL_DRIVER=smtp
MAIL_HOST=mailtrap.io
MAIL_PORT=2525
MAIL_USERNAME=null
MAIL_PASSWORD=null
MAIL_ENCRYPTION=null

PUSHER_APP_ID=
PUSHER_APP_KEY=
PUSHER_APP_SECRET=
```

./images/nginx/config/default.conf
```
server {
	listen				80;
	server_name			localhost;
	root				/mnt/app/laravel/public;
	index				index.php index.html index.htm;

	location / {
		index			index.php index.html index.htm;
		try_files		$uri $uri/ /index.php?$query_string;
	}

	location ~ \.php$ {
		fastcgi_pass	php:9000;
		fastcgi_index	index.php;
		fastcgi_param	SCRIPT_FILENAME		$document_root$fastcgi_script_name;
		include			fastcgi_params;
	}
}
```
![](image/screenshot_1485963691384.png)

```
// 重启web服务
docker-compose restart web
```

通过浏览器访问 http://192.168.99.100:8080/ 

![](image/screenshot_1485963779956.png)