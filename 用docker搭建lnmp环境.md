### 准备工作：
```sh
$ docker pull mysql
$ docker pull php:7.0.2-fpm
$ docker pull nginx
```

## MYSQL部分
#### 新建数据卷容器（存放mysql数据库文件）
```sh
$ docker run -d -v /var/lib/mysql --name mysqldata zhaojianhui/lnmp echo Data-only container for postgres
```
> -v 参数为指定的目录，挂在此数据卷容器的目录以此为准，在其他容器中使用 --volumes-from 来挂载 dbdata 容器中的数据卷。

### 启动mysql容器

简单方式
```sh
$ docker run --name mysql --volumes-from mysqldata -e MYSQL_ROOT_PASSWORD=123456 -d mysql
```
绑定映射主机端口
```sh
docker run --name mysql2 --volumes-from mysqldata2 -e MYSQL_ROOT_PASSWORD=123456 -p 127.0.0.1:3306:3306 -d mysql
```
> 注意，在启动新的数据库容器是，挂在的数据卷不能公用，否则会导致错误

###查看mysql日志
docker exec 命令能让你在一个容器中额外地运行新命令。比如你可以执行下面的命令来获得一个 bash shell

```sh
$ docker exec -it some-mysql bash
```

你可以通过查看 Docker 容器的日志获得 MySQL 服务的日志

```sh
$ docker logs some-mysql
```

mysql容器[官方文档地址](https://hub.docker.com/_/mysql/)

mysql容器[中文文档地址](https://github.com/DaoCloud/library-image/tree/master/mysql)

##nginx部分


### 新建web站点容器(存放php代码)

```sh
$ docker run -d -v /usr/share/nginx/html --name webdata zhaojianhui/lnmp echo Data-only container for postgres
```

> 经过实践，php代码还是从主机目录直接挂载比较方便，新建数据卷容器操作代码比较麻烦，更新代码不方便

###启动nginx容器
```sh
docker run --name nginx -volumes-from webdata -d -p 80:80 nginx
```

##php部分

启动php容器
```sh
docker run --name php --volumes-from webdata -d php:7.0.2-fpm
```
此时docker ps -a是就能看到容器启动情况

###整合php和nginx
从nginx容器中拷贝配置文件到主机
```sh
docker cp nginx:/etc/nginx/nginx.conf /root/nginx.conf
```
更改配置文件：
```sh
vi /root/nginx.conf
  include /etc/nginx/conf.d/*.conf;
```
> 看到此文件最后有一行是包含其他配置文件，由此可见，容器中的配置文件默认已经做好了不同nginx虚拟机的配置文件隔离，进入到nginx容器中查看后，发现/etc/nginx/conf.d/目录中已经存在一个default.conf文件，所以将此文件拷贝到宿主机上，重命名并修改正确配置之后再次拷贝到容器中。

```sh
docker cp nginx:/etc/nginx/conf.d/default.conf /root/new.conf
```
查看容器的ip地址，之后在修改nginx配置时会用到：
```sh
iptables -L -n
```

修改php配置部分正确之后保存

```sh
location / {
        root   /usr/share/nginx/html;
        index  index.php index.html index.htm;
    }
............
location ~ \.php$ {
        root           /usr/share/nginx/html/;
        fastcgi_pass   172.17.0.5:9000;
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
        include        fastcgi_params;
    }
```

再次拷贝到容器中制定目录：

```sh
docker cp /root/new.conf nginx:/etc/nginx/conf.d/
#如有必要，重启下nginx容器
docker restart nginx
```
