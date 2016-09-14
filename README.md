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

####批量删除镜像
先run一下： docker images | grep "^" | awk "{print $3}"， 看看是不是确实有  的image，如果没有，直接运行会有你现在的问题。
```sh
docker rmi $(docker images | grep "^" | awk "{print $3}")
```

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
docker build -t=zhaojianhui129/mysql:latest ./mysql/
```
启动MYSQL容器：
```sh
docker run --name mysql -v /data/mysql:/var/lib/mysql -p 3306:3306 -d zhaojianhui129/mysql:latest
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

####启动memcached容器存储session，主机端口11211，容器端口11211
```sh
docker run --name memcached -p 11211:11211 -d memcached
```

###启动redis容器,主机6379端口，容器6379
```sh
docker run --name redis -d -v /data/redis:/data -p 6379:6379 redis redis-server --appendonly yes
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
docker build -t=zhaojianhui129/php:fpm ./php7fpm/
docker build -t=zhaojianhui129/php:cli ./php7cli/
#php5
docker build -t=zhaojianhui129/php:5-fpm ./php5fpm/
```

启动php容器
【挂载主机目录形式】，不推荐
```sh
docker run --name php -v /mnt/hgfs/GIT/:/www-data/ -d zhaojianhui/lnmp:php
```
【挂载数据卷容器形式】推荐：
```sh
docker run --name php --volumes-from web --link redis:redis --link mysql:mysql -d zhaojianhui129/php:fpm
docker run --name php5 --volumes-from web --link redis:redis --link mysql:mysql -d zhaojianhui129/php:5-fpm

#cli模式
docker run -it --rm --name phpcli -v /mnt/hgfs/GIT/swooletest/:/data/swooletest/ -w /data/swooletest/ --link redis:redis --link mysql:mysql -p 9503:9503 zhaojianhui129/php:cli php timerTick.php

# memcached容器存储session
docker run --name php5 --volumes-from web --link memcached:memcached --link mysql:mysql -d zhaojianhui/lnmp:php5
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
docker run --name nginx --volumes-from web -p 80:80 --link php:php --link php5:php5 -d zhaojianhui129/nginx:latest
```
自定义挂载目录，和php的挂载目录保持一致，此方式没有挂载数据卷容器灵活：
```sh
docker run --name nginx -v /mnt/hgfs/GIT/:/www-data/  -p 80:80 -d zhaojianhui129/nginx:latest
```


####提交镜像到远程仓库
```sh
docker push zhaojianhui129/php:fpm

```