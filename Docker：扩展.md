#Docker：扩展

###安装docker-compose
```
curl -L https://github.com/docker/compose/releases/download/1.7.1/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose

chmod +x /usr/local/bin/docker-compose
```

###使用加速器下载镜像（linux环境）
```
curl -sSL https://get.daocloud.io/daotools/set_mirror.sh | sh -s http://c0b3b6a9.m.daocloud.io
// 重启docker配置才能生效
systemctl daemon-reload
systemctl restart docker && systemctl enable docker
```

###搭建laradock开发环境
参考资料：
[https://laravel-china.org/topics/3374](https://laravel-china.org/topics/3374)  
[https://github.com/nickfan/laradock](https://github.com/nickfan/laradock)

