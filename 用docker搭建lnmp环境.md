### 准备工作：
```sh
$ docker pull mysql
$ docker pull php:7.0.2-fpm
$ docker pull nginx
```

### 新建数据卷容器
#### 新建数据库容器（存放mysql数据库文件）
```sh
$ docker run -d -v /var/lib/mysql --name mysqldata zhaojianhui/lnmp echo Data-only container for postgres
```
> -v 参数为指定的目录，挂在此数据卷容器的目录以此为准，在其他容器中使用 --volumes-from 来挂载 dbdata 容器中的数据卷。
### 新建web站点容器(存放php代码)

```sh
$ docker run -d -v /data/www --name webdata zhaojianhui/lnmp echo Data-only container for postgres
```


### 启动mysql容器

```sh
$ docker run --name mysql --volumes-from mysqldata -e MYSQL_ROOT_PASSWORD=123456 -d mysql
```

###查看mysql日志
docker exec 命令能让你在一个容器中额外地运行新命令。比如你可以执行下面的命令来获得一个 bash shell

```sh
$ docker exec -it some-mysql bash
```

你可以通过查看 Docker 容器的日志获得 MySQL 服务的日志
```sh
$ docker logs some-mysql
```
>  mysql容器[官方文档地址](https://hub.docker.com/_/mysql/)

>  mysql容器[中文文档地址](https://github.com/DaoCloud/library-image/tree/master/mysql)