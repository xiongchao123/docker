# docker
docker学习，新手勿喷

###docker加速器
Docker Hub 在国外，有时候拉取 Image 极其缓慢，可以使用国内的镜像来实现加速

####阿里云
```sh
echo "DOCKER_OPTS=\"--registry-mirror=https://yourlocation.mirror.aliyuncs.com\"" | sudo tee -a /etc/default/docker
sudo service docker restart
```
> 其中 https://yourlocation.mirror.aliyuncs.com 是您在阿里云注册后的专属加速器地址
[文档地址](https://yq.aliyun.com/articles/29941)

#### DaoCloud
```sh
sudo echo “DOCKER_OPTS=\”\$DOCKER_OPTS –registry-mirror=http://your-id.m.daocloud.io -d\”” >> /etc/default/docker
sudo service docker restart
```
> 其中 http://your-id.m.daocloud.io 是您在 DaoCloud 注册后的专属加速器地址：
[文档地址](https://www.daocloud.io/)


###手动设置加速器
原理是找到启动配置文件中的ExecStart那一行，然后在启动参数那里加上registry-mirror参数来切换镜像起到加速的目的。两种方式：
> 阿里云：https://qqe07tk2.mirror.aliyuncs.com
>
> DaoCloud: http://0752ec30.m.daocloud.io

在线执行：
```sh
curl -sSL https://get.daocloud.io/daotools/set_mirror.sh | sh -s http://0752ec30.m.daocloud.io
```

手动执行：
```sh
sudo cp -bf /lib/systemd/system/docker.service /etc/systemd/system/docker.service
sudo sed -i "s|ExecStart=/usr/bin/dockerd|ExecStart=/usr/bin/dockerd --registry-mirror=https://qqe07tk2.mirror.aliyuncs.com|g" /etc/systemd/system/docker.service
sudo systemctl daemon-reload
sudo service docker restart
```

###安装docker工具
```sh
$ wget -P ~ https://github.com/yeasy/docker_practice/raw/master/_local/.bashrc_docker;
$ echo "[ -f ~/.bashrc_docker ] && . ~/.bashrc_docker" >> ~/.bashrc; source ~/.bashrc
```

####批量删除镜像
先run一下： docker images | grep "^" | awk "{print $3}"， 看看是不是确实有  的image，如果没有，直接运行会有你现在的问题。
```sh
docker rmi $(docker images | grep "^" | awk "{print $3}")
```
####1、停用全部运行中的容器:
```sh
docker stop $(docker ps -q)
```
####2、删除全部容器：
```sh
docker rm $(docker ps -aq)
```
####3、一条命令实现停用并删除容器：
```sh
docker stop $(docker ps -q) & docker rm $(docker ps -aq)
```

#设定镜像下载源
```sh
RUN echo "deb http://mirrors.aliyun.com/debian wheezy main contrib non-free\ndeb-src http://mirrors.aliyun.com/debian wheezy main contrib non-free\ndeb http://mirrors.aliyun.com/debian wheezy-updates main contrib non-free\ndeb-src http://mirrors.aliyun.com/debian wheezy-updates main contrib non-free\ndeb http://mirrors.aliyun.com/debian-security wheezy/updates main contrib non-free\ndeb-src http://mirrors.aliyun.com/debian-security wheezy/updates main contrib non-free" | tee /etc/apt/sources.list

RUN echo "deb http://mirrors.163.com/debian/ jessie main non-free contrib\ndeb http://mirrors.163.com/debian/ jessie-updates main non-free contrib\ndeb http://mirrors.163.com/debian/ jessie-backports main non-free contrib\ndeb-src http://mirrors.163.com/debian/ jessie main non-free contrib\ndeb-src http://mirrors.163.com/debian/ jessie-updates main non-free contrib\ndeb-src http://mirrors.163.com/debian/ jessie-backports main non-free contrib\ndeb http://mirrors.163.com/debian-security/ jessie/updates main non-free contrib\ndeb-src http://mirrors.163.com/debian-security/ jessie/updates main non-free contrib" | tee /etc/apt/sources.list
```

官方仓库地址：
https://hub.docker.com/explore/
> php
> [英文地址](https://hub.docker.com/_/php/)
> [中文地址](https://github.com/DaoCloud/library-image/tree/master/php)
> <br>
> mysql
> [英文地址](https://hub.docker.com/_/mysql/)
> [中文地址](https://github.com/DaoCloud/library-image/tree/master/mysql)
> <br>
> nginx
> [英文地址](https://hub.docker.com/_/nginx/)
> [中文地址](https://github.com/DaoCloud/library-image/tree/master/nginx)
>
> nginx-php-fpm
> [英文地址](https://hub.docker.com/r/richarvey/nginx-php-fpm/)
> 
> 
> <br>
> mariadb
> [英文地址](https://hub.docker.com/_/mariadb/)
>
> <br>
> redis
> [英文地址](https://hub.docker.com/_/redis/)
>
> <br>
> mongo
> [英文地址](https://hub.docker.com/_/mongo/)
> [中文地址](https://github.com/DaoCloud/library-image/tree/master/mongo)
>
> <br>
> memcached
> [英文地址](https://hub.docker.com/_/memcached/)
>
> <br>
> rabbitmq
> [英文地址](https://hub.docker.com/_/rabbitmq/)
> [中文地址](https://github.com/DaoCloud/library-image/tree/master/rabbitmq)
>
> <br>
> composer
> [英文地址](https://hub.docker.com/_/composer/)
>
> <br>
> ubuntu
> [英文地址](https://hub.docker.com/_/ubuntu/)
> [中文地址](https://github.com/DaoCloud/library-image/tree/master/ubuntu)
>
> <br>
> centos
> [英文地址](https://hub.docker.com/_/centos/)
> [中文地址](https://github.com/DaoCloud/library-image/tree/master/centos)
>
><br>
> HAProxy
> HAProxy是一个使用C语言编写的自由及开放源代码软件[1]，其提供高可用性、负载均衡，以及基于TCP和HTTP的应用程序代理。
> [英文地址](https://hub.docker.com/_/haproxy/)

><br>
>elasticsearch
>[英文地址](https://hub.docker.com/_/elasticsearch/)
>

####生成mysql镜像部分
```sh
//mysql5.7
docker build -t=zhaojianhui129/mysql:5.7 ./mysql5.7/
//mysql8
docker build -t=zhaojianhui129/mysql:8.0 ./mysql8.0/
```
启动MYSQL容器：
```sh
//mysql5.7
docker run --name mysql -v /data/mysql:/var/lib/mysql -p 3306:3306 -d zhaojianhui129/mysql:latest
//mysql8
docker run --name mysql -v /data/mysql:/var/lib/mysql -p 3306:3306 -d zhaojianhui129/mysql:8 --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci
```
//官方原版启动（5.7）
```sh
docker run --name mysql -e MYSQL_ROOT_PASSWORD=123456 -e MYSQL_DATABASE=test -e MYSQL_USER=qianxun -e MYSQL_PASSWORD=123456 -v /data/mysql:/var/lib/mysql -p 3306:3306 -d mysql
```

//官方原本启动（5.6）
```sh
docker run --name mysql56 -e MYSQL_ROOT_PASSWORD=123456 -e MYSQL_DATABASE=test -e MYSQL_USER=qianxun -e MYSQL_PASSWORD=123456 -v /data/mysql56:/var/lib/mysql -p 3307:3306 -d mysql:5.6
```

//官方原版启动（8.0）
```sh
docker run --name mysql -e MYSQL_ROOT_PASSWORD=123456 -e MYSQL_DATABASE=test -e MYSQL_USER=qianxun -e MYSQL_PASSWORD=123456 -v /data/mysql:/var/lib/mysql -p 3306:3306 -d mysql:8 --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci
```


新建超级管理员账号查看Dockerfiles信息

####启动memcached容器存储session，主机端口11211，容器端口11211
```sh
docker run --name memcached -p 11211:11211 -d memcached
```

###启动redis容器,主机6379端口，容器6379
```sh
docker run --name redis -d -v /data/redis:/data -p 6379:6379 redis redis-server --appendonly yes
```

###启动mongo容器，
```sh
docker run --name mongodb -v /data/mongodb:/data/db -d mongo:latest
```

####挂载一个主机目录作为代码数据卷容器
```sh
#自己脑洞大开想的，因为ubuntu镜像比较小
docker run -d -v /mnt/hgfs/website/:/www-data/ --name web ubuntu echo Data-only container for postgres
#手册上新建数据卷容器
docker run -d -v /mnt/hgfs/GIT/:/www-data/ --name web training/postgres echo Data-only container for postgres
```

###创建rabbitmq容器并创建管理工具
```sh
#不带管理界面的
docker run -d --hostname rabbitmq_server --name rabbitmq rabbitmq:3
#带管理界面的
docker run -d --hostname rabbitmq_server --name rabbitadmin -p 8080:15672 rabbitmq:3-management
##默认账号密码：
-e RABBITMQ_DEFAULT_USER=user -e RABBITMQ_DEFAULT_PASS=password
##默认host
-e RABBITMQ_DEFAULT_VHOST=my_vhost
```


####生成php镜像部分
> 有效的php扩展列表为:
> bcmath bz2 calendar ctype curl dba dom enchant exif fileinfo filter ftp gd gettext gmp hash iconv imap interbase intl json ldap mbstring mcrypt mysqli oci8 odbc opcache pcntl pdo pdo_dblib pdo_firebird pdo_mysql pdo_oci pdo_odbc pdo_pgsql pdo_sqlite pgsql phar posix pspell readline recode reflection session shmop simplexml snmp soap sockets spl standard sysvmsg sysvsem sysvshm tidy tokenizer wddx xml xmlreader xmlrpc xmlwriter xsl zip

```sh
#php7.0
docker build -t=zhaojianhui129/php:php7.0-fpm ./php7.0-fpm/
docker build -t=zhaojianhui129/php:php7.0-cli ./php7.0-cli/
#php7.1
docker build -t=zhaojianhui129/php:php7.1-fpm ./php7.1-fpm/
docker build -t=zhaojianhui129/php:php7.1-cli ./php7.1-cli/
docker build -t=zhaojianhui129/php:php7.1-apache ./php7.1-apache/
#php7.2
docker build -t=zhaojianhui129/php:php7.2-fpm ./php7.2-fpm/
docker build -t=zhaojianhui129/php:php7.2-cli ./php7.2-cli/
#php5
docker build -t=zhaojianhui129/php:5-fpm ./php5fpm/
```

启动php容器
【挂载主机目录形式】，不推荐
```sh
docker run --name php -v /mnt/hgfs/GIT/:/www-data/ -d zhaojianhui/lnmp:php

docker run --name php5 -v /home/qianxun/website/dflpvmkt/:/home/qianxun/website/dflpvmkt/ -d zhaojianhui129/php:5-fpm
```
【挂载数据卷容器形式】推荐：
```sh
docker run --name php --volumes-from web --link redis:redis_server --link mysql:mysql_server --dns=211.136.192.6 --dns=8.8.8.8 --dns=8.8.4.4 -d zhaojianhui129/php:fpm
docker run --name php5 --volumes-from web --link redis:redis_server --link mysql:mysql_server --dns=211.136.192.6 --dns=8.8.8.8 --dns=8.8.4.4 -d zhaojianhui129/php:5-fpm

#cli模式
docker run -it --rm --name phpcli -v /mnt/hgfs/GIT/swooletest/:/data/swooletest/ -w /data/swooletest/ --link redis:redis_server --link mysql:mysql_server --dns=211.136.192.6 --dns=8.8.8.8 --dns=8.8.4.4 -p 9503:9503 zhaojianhui129/php:cli php timerTick.php

# memcached容器存储session
docker run --name php5 --volumes-from web --link memcached:memcached --link mysql:mysql_server -d zhaojianhui/lnmp:php5
```



####生成nginx镜像部分
使用docker-ip查看php容器的ip地址，然后配置好./nginx/vhosts目录下的配置文件，然后生成自定义容器，如下;
> 注意nginx配置文件的fastcgi_pass参数部分，已经改为了php容器名称，使用此方法可以不用担心容器ip随时切换的问题。
> 需要注意的是，我们将fastcgi_pass的值从127.0.0.1:9000改为了phpfpm:9000，这里的phpfpm是域名，在nginx容器的/etc/hosts文件中自动配置为phpfpm容器的访问IP。

```sh
docker build -t=zhaojianhui129/nginx:latest ./nginx/
```

######启动nginx容器：
以php容器的挂载目录为准，挂载同样的目录,使用容器互联的方式，不担心容器IP会变化：
```sh
docker run --name nginx --volumes-from web -p 80:80 --link php:php_server --link php5:php5_server -d zhaojianhui129/nginx:latest
```
自定义挂载目录，和php的挂载目录保持一致，此方式没有挂载数据卷容器灵活：
```sh
docker run --name nginx -v /mnt/hgfs/GIT/:/www-data/  -p 80:80 -d zhaojianhui129/nginx:latest
```


####提交镜像到远程仓库
```sh
docker push zhaojianhui129/php:fpm
```
###命令集合
```sh
docker build -t=zhaojianhui129/mysql:8 ./mysql8/
docker build -t=zhaojianhui129/php:php7.1-fpm ./php7.1-fpm/
docker build -t=zhaojianhui129/php:php7.1-cli ./php7.1-cli/
docker build -t=zhaojianhui129/php:5-fpm ./php5fpm/
docker build -t=zhaojianhui129/nginx:latest ./nginx/
#数据卷容器
docker run -d -v /home/qianxun/website/:/www-data/ --name web ubuntu echo Data-only container for postgres
#memcached：
docker run --name memcached -p 11211:11211 -d memcached
#redis：
docker run --name redis -d -v /data/redis:/data -p 6379:6379 redis redis-server --appendonly yes
#rabbitmq
docker run -d --hostname rabbitmq_server --name rabbitmq -p 8081:15672 rabbitmq:3-management
#mysql
docker run --name mysql -v /data/mysql:/var/lib/mysql -p 3306:3306 -d zhaojianhui129/mysql:8 --character-set-server=utf8 --collation-server=utf8_general_ci
#php:
docker run --name php --volumes-from web --link memcached:memcached_host --link redis:redis_host --link mysql:mysql_host --link rabbitmq:rabbitmq_host -d zhaojianhui129/php:fpm
#nginx
docker run --name nginx --volumes-from web --link php:php_server -d zhaojianhui129/nginx:latest
#wordpress
docker run --name wordpress --link mysql:mysql -e WORDPRESS_DB_USER=root -e WORDPRESS_DB_PASSWORD=123456 -e WORDPRESS_DB_NAME=wordpress -p 8082:80 -d wordpress:php7.1-apache
#elasticsearch
docker run --name elas -p 9200:9200 -d -v "$PWD/esdata":/usr/share/elasticsearch/data elasticsearch -Etransport.host=0.0.0.0 -Ediscovery.zen.minimum_master_nodes=1
```



##王滔企业官网启动命令
###apache
```
docker run -d -p 80:80 --name app -v "$PWD":/var/www -v "$PWD"/publc:/var/www/html zhaojianhui129/php:php7.1-apache
```
###nginx+php-fpm
```
docker run -d -v /data/wwwroot/:/data/wwwroot/ --name wwwroot ubuntu echo Data-only container for postgres
docker build -t=zhaojianhui129/php:php7.1-fpm ./php7.1-fpm/
docker run --name php --volumes-from wwwroot --link mysql:mysql_host -d zhaojianhui129/php:php7.1-fpm
docker run --name nginx --volumes-from wwwroot --link php:php_server -d zhaojianhui129/nginx:latest
```