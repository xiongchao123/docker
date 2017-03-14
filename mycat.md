#Mycat使用配置

启动MySQL实例
```sh
docker run --name mysqlServer1 -v /data/mysqlServer1:/var/lib/mysql -d zhaojianhui129/mysql:8 --character-set-server=utf8 --collation-server=utf8_general_ci
docker run --name mysqlServer2 -v /data/mysqlServer2:/var/lib/mysql -d zhaojianhui129/mysql:8 --character-set-server=utf8 --collation-server=utf8_general_ci
docker run --name mysqlServer3 -v /data/mysqlServer3:/var/lib/mysql -d zhaojianhui129/mysql:8 --character-set-server=utf8 --collation-server=utf8_general_ci
docker run --name mysqlServer4 -v /data/mysqlServer4:/var/lib/mysql -d zhaojianhui129/mysql:8 --character-set-server=utf8 --collation-server=utf8_general_ci
docker run --name mysqlServer5 -v /data/mysqlServer5:/var/lib/mysql -d zhaojianhui129/mysql:8 --character-set-server=utf8 --collation-server=utf8_general_ci
docker run --name mysqlServer6 -v /data/mysqlServer6:/var/lib/mysql -d zhaojianhui129/mysql:8 --character-set-server=utf8 --collation-server=utf8_general_ci
```
查看IP:
```sh
docker-ip mysqlServer1
docker-ip mysqlServer2
docker-ip mysqlServer3
docker-ip mysqlServer4
docker-ip mysqlServer5
docker-ip mysqlServer6
```
启动容器：
```sh
docker start mysqlServer1
docker start mysqlServer2
docker start mysqlServer3
docker start mysqlServer4
docker start mysqlServer5
docker start mysqlServer6
```

双主复制：
mysqlServer1配置：
```sh
docker-enter mysqlServer1
```
```sh
apt-get install vim -y
vim /etc/mysql/my.cnf
###添加以下代码
##---------
log-bin=mysql-bin
server_id=1
#以下部分为在原基础上新增的内容
relay-log = mysql-relay-bin
replicate-wild-ignore-table=mysql.%
replicate-wild-ignore-table=test.%
replicate-wild-ignore-table=information_schema.%
#---------
```

mysqlServer2配置：
```sh
docker-enter mysqlServer2
```
```sh
apt-get install vim -y
vim /etc/mysql/my.cnf
###添加以下代码
#---------
server_id=2
relay-log=mysql-relay-bin
#以下部分为在原基础上新增的内容
log-bin=mysql-bin
replicate-wild-ignore-table=mysql.%
replicate-wild-ignore-table=test.%
replicate-wild-ignore-table=information_schema.%
#---------
```

在mysqlServer1服务器上指定mysqlServer2为自己的主服务器并开启slave
```sh
docker-enter mysqlServer1
```
