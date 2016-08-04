# docker
docker学习，新手勿喷

docker加速器
[文档地址](https://www.daocloud.io/mirror#accelerator-doc)

官方仓库地址：
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
> <br>
> redis
> [英文地址](https://hub.docker.com/_/redis/)
> 
> <br>
> memcached
> [英文地址](https://hub.docker.com/_/memcached/)
> 

####生成mysql镜像部分
```sh
docker build -t=daocloud.io/zhaojianhui129/mysql:latest ./mysql/
```
启动MYSQL容器：
```sh
docker run --name mysql -v /data/mysql:/var/lib/mysql -p 3306:3306 -d zhaojianhui/lnmp:mysql
```
//官方原版启动（5.7）
```sh
docker run --name mysql -e MYSQL_ROOT_PASSWORD=123456 -e MYSQL_DATABASE=test -e MYSQL_USER=qianxun -e MYSQL_PASSWORD=123456 -v /data/mysql:/var/lib/mysql -p 3306:3306 -d mysql
```

//官方原本启动（5.6）
```sh
docker run --name mysql56 -e MYSQL_ROOT_PASSWORD=123456 -e MYSQL_DATABASE=test -e MYSQL_USER=qianxun -e MYSQL_PASSWORD=123456 -v /data/mysql56:/var/lib/mysql -p 3307:3306 -d mysql:5.6
```

新建超级管理员账号查看Dockerfiles信息

####启动memcached容器存储session
```sh
docker run --name memcached -d memcached
```

###启动redis容器,主机6380端口，容器6379
```sh
docker run --name redis -d -v /data/redis:/data -p 6380:6379 redis
```

####挂载一个主机目录作为代码数据卷容器
```sh
#自己脑洞大开想的，因为ubuntu镜像比较小
docker run -d -v /mnt/hgfs/website/:/www-data/ --name web ubuntu echo Data-only container for postgres
#手册上新建数据卷容器
docker run -d -v /mnt/hgfs/GIT/:/www-data/ --name web training/postgres echo Data-only container for postgres
```


####生成php镜像部分
> 有效的php扩展列表为:
> bcmath bz2 calendar ctype curl dba dom enchant exif fileinfo filter ftp gd gettext gmp hash iconv imap interbase intl json ldap mbstring mcrypt mysqli oci8 odbc opcache pcntl pdo pdo_dblib pdo_firebird pdo_mysql pdo_oci pdo_odbc pdo_pgsql pdo_sqlite pgsql phar posix pspell readline recode reflection session shmop simplexml snmp soap sockets spl standard sysvmsg sysvsem sysvshm tidy tokenizer wddx xml xmlreader xmlrpc xmlwriter xsl zip

```sh
#php7
docker build -t=daocloud.io/zhaojianhui129/php:fpm ./php7fpm/
#php5
docker build -t=daocloud.io/zhaojianhui129/php:5-fpm ./php5fpm/
```

启动php容器
【挂载主机目录形式】，不推荐
```sh
docker run --name php -v /mnt/hgfs/GIT/:/www-data/ -d zhaojianhui/lnmp:php
```
【挂载数据卷容器形式】推荐：
```sh
docker run --name php --volumes-from web -d daocloud.io/zhaojianhui129/php:fpm
docker run --name php5 --volumes-from web -d daocloud.io/zhaojianhui129/php:5-fpm
# memcached容器存储session
docker run --name php5 --volumes-from web --link memcached:memcached -d zhaojianhui/lnmp:php5
```


####生成nginx镜像部分
使用docker-ip查看php容器的ip地址，然后配置好./nginx/vhosts目录下的配置文件，然后生成自定义容器，如下;
> 注意nginx配置文件的fastcgi_pass参数部分，已经改为了php容器名称，使用此方法可以不用担心容器ip随时切换的问题。
> 需要注意的是，我们将fastcgi_pass的值从127.0.0.1:9000改为了phpfpm:9000，这里的phpfpm是域名，在nginx容器的/etc/hosts文件中自动配置为phpfpm容器的访问IP。

```sh
docker build -t=daocloud.io/zhaojianhui129/nginx:latest ./nginx/
```

######启动nginx容器：
以php容器的挂载目录为准，挂载同样的目录,使用容器互联的方式，不担心容器IP会变化：
```sh
docker run --name nginx --volumes-from web  -p 80:80 --link php:php --link php5:php5 --link phplaravel:phplaravel -d daocloud.io/zhaojianhui129/nginx:latest

docker run --name nginx --volumes-from web  -p 80:80 --link php:php --link php5:php5 -d daocloud.io/zhaojianhui129/nginx:latest
```
自定义挂载目录，和php的挂载目录保持一致，此方式没有挂载数据卷容器灵活：
```sh
docker run --name nginx -v /mnt/hgfs/GIT/:/www-data/  -p 80:80 -d daocloud.io/zhaojianhui129/nginx:latest
```