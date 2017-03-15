#Mycat双主互备

###参考地址：
http://www.linuxidc.com/Linux/2017-02/141173.htm

###启动MySQL实例
```sh
docker run --name mysqlServer1 -v /data/mysqlServer1:/var/lib/mysql -d zhaojianhui129/mysql:8 --character-set-server=utf8 --collation-server=utf8_general_ci
docker run --name mysqlServer2 -v /data/mysqlServer2:/var/lib/mysql -d zhaojianhui129/mysql:8 --character-set-server=utf8 --collation-server=utf8_general_ci
docker run --name mysqlServer3 -v /data/mysqlServer3:/var/lib/mysql -d zhaojianhui129/mysql:8 --character-set-server=utf8 --collation-server=utf8_general_ci
docker run --name mysqlServer4 -v /data/mysqlServer4:/var/lib/mysql -d zhaojianhui129/mysql:8 --character-set-server=utf8 --collation-server=utf8_general_ci
docker run --name mysqlServer5 -v /data/mysqlServer5:/var/lib/mysql -d zhaojianhui129/mysql:8 --character-set-server=utf8 --collation-server=utf8_general_ci
docker run --name mysqlServer6 -v /data/mysqlServer6:/var/lib/mysql -d zhaojianhui129/mysql:8 --character-set-server=utf8 --collation-server=utf8_general_ci
```
###查看IP:
```sh
docker-ip mysqlServer1
docker-ip mysqlServer2
docker-ip mysqlServer3
docker-ip mysqlServer4
docker-ip mysqlServer5
docker-ip mysqlServer6
```
###启动容器：
```sh
docker start mysqlServer1
docker start mysqlServer2
docker start mysqlServer3
docker start mysqlServer4
docker start mysqlServer5
docker start mysqlServer6
```

###双主复制：
####mysqlServer1配置：
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

####mysqlServer2配置：
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

###重启：
```sh
docker restart mysqlServer1
docker restart mysqlServer2
```

###在mysqlServer1服务器上指定mysqlServer2为自己的主服务器并开启slave
```sh
---
docker-enter mysqlServer1
---
# 在mysqlServer2上查看当前master信息，并建立复制用户
mysql -u root -p
mysql> show master status;
mysql> grant replication slave on *.* to 'repl_user'@'172.17.0.%' identified by '123456';
mysql> flush privileges;

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
mysql> change master to master_host='172.17.0.9',master_user='repl_user',master_password='123456',master_log_file='mysql-bin.000001',master_log_pos=154;
mysql> start slave;

#在mysqlServer2上指定mysqlServer1为自己的主服务器，开启slave
---
docker-enter mysqlServer2
---
mysql -u root -p
mysql> change master to master_host='172.17.0.8',master_user='repl_user',master_password='123456',master_log_file='mysql-bin.000001',master_log_pos=154;
mysql> start slave;


```

###检验双主互备
①通过分别在两台服务器上使用`show slave status\G`，查询主库信息以及IO进程、SQL进程工作状态。若两台服务器的查询结果都为Slave_IO_Running: Yes，Slave_SQL_Running: Yes；则表示当前双主互备状态正常。

②在mysqlServer1数据库上建库建表，检查mysqlServer2上是否同步正常；然后在mysqlServer2上建库建表，检查mysqlServer1上是否同步正常。 


#keepalived高可用部署
1、keepalived编译安装
#编译操作两台机器一样
```sh
apt-get install wget gcc make libssl-dev -y
wget http://www.keepalived.org/software/keepalived-1.3.4.tar.gz
tar -zxf keepalived-1.3.4.tar.gz
cd keepalived-1.3.4
./configure --prefix=/usr/local/keepalived --with-kernel-dir=/usr/src/kernels/2.6.18-164.el5-i686
make && make install
mkdir /etc/keepalived
vi /etc/keepalived/keepalived.conf
###配置以下：
! Configuration File for keepalived
global_defs {
  notification_email {
    zhaojianhui129@163.com
  }
  notification_email_from Alexandre.Cassen@firewall.loc
  smtp_server 127.0.0.1
  smtp_connect_timeout 30
  router_id MySQLHA_DEVEL
}
vrrp_script check_mysqld {
    script "/etc/keepalived/mysqlcheck/check_slave.sh" #定义检测mysql复制状态的脚本
    interval 2
    }
vrrp_instance HA_1 {
    state BACKUP                #两台mysql服务器均配置为BACKUP
    interface eth0
    virtual_router_id 80
    priority 100  #优先级，另一台改为90
    advert_int 2
    nopreempt #不抢占模式，只在优先级高的机器上设置即可，备用节点不加此参数
    authentication {
        auth_type PASS
        auth_pass qweasdzxc
    }
    
    track_script {
        check_mysqld #调用mysql检测脚本
    }
    virtual_ipaddress {
        172.17.0.60/24 dev eth0    #mysql的对外服务IP，即VIP
    }
}

```
检测脚本
```sh
mkdir /etc/keepalived/mysqlcheck
vim /etc/keepalived/mysqlcheck/check_slave.sh
###输入以下
#!/bin/bash
#This scripts is check for Mysql Slave status

Mysqlbin=mysql
user=root
pw='123456'
port=3306
host=127.0.0.1
sbm=120
 
#Check for $Mysqlbin
if [ ! -f $Mysqlbin ];then
        echo 'Mysqlbin not found,check the variable Mysqlbin'
        exit 99
fi
 
#Get Mysql Slave Status
IOThread=`$Mysqlbin -h $host -P $port -u$user -p$pw -e 'show slave status\G'  2>/dev/null|grep 'Slave_IO_Running:'|awk '{print $NF}'`
SQLThread=`$Mysqlbin -h $host -P $port -u$user -p$pw -e 'show slave status\G' 2>/dev/null|grep 'Slave_SQL_Running:'|awk '{print $NF}'`
SBM=`$Mysqlbin -h $host -P $port -u$user -p$pw -e 'show slave status\G' 2>/dev/null|grep 'Seconds_Behind_Master:'|awk '{print $NF}'`
 
#Check if the mysql run
if [[ -z "$IOThread" ]];then
        exit 1
fi
 
#Check if the thread run 
if [[ "$IOThread" = "No" || "$SQLThread" = "No" ]];then
        exit 1
        elif [[ $SBM -ge $sbm ]];then
                exit 1
        else
                exit 0
fi
```
#启动
```sh
/usr/local/keepalived/sbin/keepalived -D
ps -aux | grep keepalive
#重启
/usr/local/keepalived/sbin/keepalived -d -D -S 0
```

#在mysqlServer1上查看，发现此时vip在此机器,在mysqlServer2上查看，并无VIP
```sh
ip addr
```



#多从服务器配置，注意ip指向虚拟IP即可
####mysqlServer3配置：
```sh
docker-enter mysqlServer3
```
```sh
vim /etc/mysql/my.cnf
###添加以下代码
##---------
server_id=3
relay-log = mysql-relay-bin
#---------
docker restart mysqlServer3
docker-enter mysqlServer3
mysql -u root -p
mysql> change master to master_host='172.17.0.60',master_user='repl_user',master_password='123456',master_log_file='mysql-bin.000002',master_log_pos=154;
mysql> start slave;
mysql> show slave status\G;
```
####mysqlServer4配置：
```sh
docker-enter mysqlServer4
```
```sh
vim /etc/mysql/my.cnf
###添加以下代码
##---------
server_id=4
relay-log = mysql-relay-bin
#---------
docker restart mysqlServer4
docker-enter mysqlServer4
mysql -u root -p
mysql> change master to master_host='172.17.0.60',master_user='repl_user',master_password='123456',master_log_file='mysql-bin.000002',master_log_pos=154;
mysql> start slave;
mysql> show slave status\G;
```

至此，主主备份，一主多从配置完成，

###JDK安装
登录网址：http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html
选择对应jdk版本下载。
```sh
sudo mkdir /usr/java
sudo cp ~/下载/jdk-8u20-linux-x64.tar.gz /usr/java/
cd /usr/java/
sudo tar zxvf jdk-8u20-linux-x64.tar.gz
sudo vim /etc/profile
#最后一行添加如下内容
#------
#JAVA
JAVA_HOME=/usr/java/jdk1.8.0_20
CLASSPATH=$JAVA_HOME/lib/
PATH=$PATH:$JAVA_HOME/bin
export PATH JAVA_HOME CLASSPATH
#-------
#重启机器或
source /etc/profile
java -version
```


MyCAT 在 Linux 中部署启动时,首先需要在 Linux 系统的环境变量中配置 MYCAT_HOME,操作方式如下:
1) vi /etc/profile,在系统环境变量文件中增加 MYCAT_HOME=/usr/local/Mycat
2) 执行 source /etc/profile 命令,使环境变量生效。

#mycat全部配置完成才能正常启动
```sh
./mycat console
./mycat start
./mycat restart
```
端口：127.0.0.1:9066
账号密码为server.xml配置的账号密码。
配置完mycat后修改表结构或者新增表需要在连接mycat端口进行操作，mycat会自动按照数据节点规则创建表。