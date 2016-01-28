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
### 新建web站点容器(存放php代码)

```sh
$ docker run -d -v /data/www --name webdata zhaojianhui/lnmp echo Data-only container for postgres
```


### 启动mysql容器

```sh
$ docker run --name mysql --volumes-from mysqldata -e MYSQL_ROOT_PASSWORD=123456 -d mysql
```