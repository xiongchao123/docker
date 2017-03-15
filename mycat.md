#Mycat使用配置

参考地址：
http://www.linuxidc.com/Linux/2017-02/141173.htm

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

重启：
```sh
docker restart mysqlServer1
docker restart mysqlServer2
```

在mysqlServer1服务器上指定mysqlServer2为自己的主服务器并开启slave
```sh
---
docker-enter mysqlServer2
---
# 在mysqlServer2上查看当前master信息，并建立复制用户
mysql -u root -p
mysql> show master status;
mysql> grant replication slave on *.* to 'repl_user'@'172.17.0.%' identified by '123456';
mysql> flush privileges;

#在mysqlServer1上指定mysqlServer2为自己的主服务器，开启slave
---
docker-enter mysqlServer1
---
####ip查看好
mysql -u root -p
mysql> change master to master_host='172.17.0.9',master_user='repl_user',master_password='123456',master_log_file='mysql-bin.000001',master_log_pos=493;
mysql> start slave;

#在mysqlServer2上指定mysqlServer1为自己的主服务器，开启slave
---
docker-enter mysqlServer2
---
mysql -u root -p
mysql> change master to master_host='172.17.0.8',master_user='repl_user',master_password='123456',master_log_file='mysql-bin.000001',master_log_pos=493;
mysql> start slave;


```

检验双主互备
①通过分别在两台服务器上使用show slave status\G，查询主库信息以及IO进程、SQL进程工作状态。若两台服务器的查询结果都为Slave_IO_Running: Yes，Slave_SQL_Running: Yes；则表示当前双主互备状态正常。

②在mysqlServer1数据库上建库建表，检查mysqlServer2上是否同步正常；然后在mysqlServer2上建库建表，检查mysqlServer1上是否同步正常。 

