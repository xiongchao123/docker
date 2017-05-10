#利用haproxy实现负载均衡

#参考地址：
http://www.cnblogs.com/xibei666/p/5877548.html

可以使用多台haproxy同时运行，利用keeplived来保证可用性。

![haproxy拓扑图](./images/haproxy.jpg)


###一、haproxy下载
http://www.haproxy.org/#down

###二、安装haproxy
查看内核版本
```sh
uname -r
uname -o
```
解压haproxy，并安装
```sh
wget http://www.haproxy.org/download/1.7/src/haproxy-1.7.3.tar.gz
tar zxvf haproxy-1.7.3.tar.gz
cd haproxy-1.7.3/
make TARGET=linux2628 CPU=x86_64 PREFIX=/usr/local/haproxy
sudo make install PREFIX=/usr/local/haproxy

#参数说明：
#TARGET=linux3100
#使用uname -r查看内核，如：2.6.18-371.el5，此时该参数就为linux26
#kernel 大于2.6.28的用：TARGET=linux2628
#CPU=x86_64   #使用uname -r查看系统信息，如x86_64 x86_64 x86_64 GNU/Linux，此时该参数就为x86_64
#PREFIX=/usr/local/haprpxy   #/usr/local/haprpxy为haprpxy安装路径
```

###安装成功后，查看版本
```sh
/usr/local/haproxy/sbin/haproxy -v
```
复制haproxy文件到/usr/sbin下
因为下面的haproxy.init启动脚本默认会去/usr/sbin下找，当然你也可以修改，不过比较麻烦。
```sh
sudo cp /usr/local/haproxy/sbin/haproxy /usr/sbin/
```
复制haproxy脚本，到/etc/init.d下
```sh
sudo cp ./examples/haproxy.init /etc/init.d/haproxy
sudo chmod 755 /etc/init.d/haproxy
```
我们可以查看一下这个haproxy.init文件
```sh
#$BASENAME默认就是haproxy
BASENAME=`basename $0`
if [ -L $0 ]; then
BASENAME=`find $0 -name $BASENAME -printf %l`
BASENAME=`basename $BASENAME`
fi
 
#执行文件路径
BIN=/usr/sbin/$BASENAME
#配置文件路径
CFG=/etc/$BASENAME/$BASENAME.cfg
#pid文件路径
PIDFILE=/var/run/$BASENAME.pid
#锁文件路径
LOCKFILE=/var/lock/subsys/$BASENAME
```
创建系统账号
```sh
useradd -r haproxy
```

####启动多个nginx容器测试
```sh
docker run --name nginx1 -d nginx:latest
docker run --name nginx2 -d nginx:latest
docker run --name nginx3 -d nginx:latest
docker-ip nginx1
docker-ip nginx2
docker-ip nginx3

###调整服务器显示内容
docker-enter nginx1
cd /usr/share/nginx/html
echo "Web Server1111111" > index.html
exit

docker-enter nginx2
cd /usr/share/nginx/html
echo "Web Server22222222" > index.html
exit

docker-enter nginx3
cd /usr/share/nginx/html
echo "Web Server3333333333" > index.html
exit
```
###创建HAProxy运行账户和组
```sh
#添加haproxy组
sudo groupadd haproxy
#创建nginx运行账户haproxy并加入到haproxy组，不允许haproxy用户直接登录系统
sudo useradd -g haproxy haproxy -s /bin/false
```
###创建配置文件
```sh
#创建配置文件目录
sudo mkdir -p  /usr/local/haproxy/conf
#创建配置文件目录
sudo mkdir -p /etc/haproxy
#创建配置文件
sudo touch  /usr/local/haproxy/conf/haproxy.cfg
#添加配置文件软连接
sudo ln -s  /usr/local/haproxy/conf/haproxy.cfg   /etc/haproxy/haproxy.cfg
#拷贝错误页面
sudo cp -r ~/下载/haproxy-1.7.3/examples/errorfiles/ /usr/local/haproxy/errorfiles
#添加软连接
sudo ln -s /usr/local/haproxy/errorfiles/ /etc/haproxy/errorfiles
#创建日志文件目录
sudo mkdir -p  /usr/local/haproxy/log
#创建日志文件
sudo touch  /usr/local/haproxy/log/haproxy.log
#添加软连接
sudo ln -s  /usr/local/haproxy/log/haproxy.log  /var/log/haproxy.log
#拷贝开机启动文件
sudo cp ~/下载/haproxy-1.7.3/examples/haproxy.init /etc/init.d/haproxy 
#添加脚本执行权限
sudo chmod +x /etc/init.d/haproxy
#设置开机启动
chkconfig haproxy on
#添加软连接
sudo ln -s  /usr/local/haproxy/sbin/haproxy  /usr/sbin
```

###4、配置haproxy.cfg参数
```sh
sudo cp /usr/local/haproxy/conf/haproxy.cfg /usr/local/haproxy/conf/haproxy.cfg.bak
sudo gedit /usr/local/haproxy/conf/haproxy.cfg
#-------------------------------------------------
#---------------------------------------------------------------------
# Global settings
#---------------------------------------------------------------------
global
    log    127.0.0.1 local2          ###[err warning info debug] 
    chroot  /usr/local/haproxy
    pidfile  /var/run/haproxy.pid   ###haproxy的pid存放路径,启动进程的用户必须有权限访问此文件 
    maxconn  4000                   ###最大连接数，默认4000
    user   haproxy
    group   haproxy
    daemon                          ###创建1个进程进入deamon模式运行。此参数要求将运行模式设置为"daemon"
 
#---------------------------------------------------------------------
# common defaults that all the 'listen' and 'backend' sections will 
# use if not designated in their block
#---------------------------------------------------------------------
defaults
    mode   http             ###默认的模式mode { tcp|http|health }，tcp是4层，http是7层，health只会返回OK
    log    global           ###采用全局定义的日志
    option  dontlognull     ###不记录健康检查的日志信息
    option  httpclose       ###每次请求完毕后主动关闭http通道 
    option  httplog         ###日志类别http日志格式 
    option  forwardfor      ###如果后端服务器需要获得客户端真实ip需要配置的参数，可以从Http Header中获得客户端ip  
    option  redispatch      ###serverId对应的服务器挂掉后,强制定向到其他健康的服务器
    timeout connect 10000   #default 10 second timeout if a backend is not found
    timeout client 300000   ###客户端连接超时
    timeout server 300000   ###服务器连接超时
    maxconn     60000       ###最大连接数
    retries     3           ###3次连接失败就认为服务不可用，也可以通过后面设置 
####################################################################
listen stats
        bind 0.0.0.0:1080           #监听端口  
        stats refresh 30s           #统计页面自动刷新时间  
        stats uri /stats            #统计页面url  
        stats realm Haproxy Manager #统计页面密码框上提示文本  
        stats auth admin:admin      #统计页面用户名和密码设置  
        #stats hide-version         #隐藏统计页面上HAProxy的版本信息
#---------------------------------------------------------------------
# main frontend which proxys to the backends
#---------------------------------------------------------------------
frontend main
    bind 0.0.0.0:8090
    acl url_static path_beg    -i /static /images /javascript /stylesheets
    acl url_static path_end    -i .jpg .gif .png .css .js
 
    use_backend static if url_static     ###满足策略要求，则响应策略定义的backend页面
    default_backend   dynamic            ###不满足则响应backend的默认页面

#---------------------------------------------------------------------
# static backend for serving up images, stylesheets and such
#---------------------------------------------------------------------

backend static
    balance     roundrobin                 ###负载均衡模式轮询
    server      static 127.0.0.1:8090 check ###后端服务器定义

backend dynamic
    balance    roundrobin
    server         websrv1 172.17.0.8:80 check maxconn 2000
    server         websrv2 172.17.0.9:80 check maxconn 2000
    server         websrv3 172.17.0.10:80 check maxconn 2000
 
#---------------------------------------------------------------------
# round robin balancing between the various backends
#---------------------------------------------------------------------
#errorloc  503  http://www.osyunwei.com/404.html
errorfile 403 /etc/haproxy/errorfiles/403.http
errorfile 500 /etc/haproxy/errorfiles/500.http
errorfile 502 /etc/haproxy/errorfiles/502.http
errorfile 503 /etc/haproxy/errorfiles/503.http
errorfile 504 /etc/haproxy/errorfiles/504.http
#-------------------------------------------------
```
###启动haproxy
```sh
sudo /usr/local/haproxy/sbin/haproxy -f /usr/local/haproxy/conf/haproxy.cfg
```
###重启haproxy
```sh
sudo /usr/local/haproxy/sbin/haproxy -f /usr/local/haproxy/conf/haproxy.cfg -st `cat /var/run/haproxy.pid`
```
###停止
```sh
sudo killall haproxy
```
###5、设置HAProxy日志
```sh
vi  /etc/syslog.conf  #编辑，在最下边增加
# haproxy.log
local0.*          /var/log/haproxy.log
local3.*          /var/log/haproxy.log
:wq! #保存退出
vi  /etc/sysconfig/syslog   #编辑修改
SYSLOGD_OPTIONS="-r -m 0"   #接收远程服务器日志
:wq! #保存退出
service syslog restart  #重启syslog
```
###6.浏览器打开haproxy的监控页面
//说明：1080即haproxy配置文件中监听端口，stats 即haproxy配置文件中的监听名称
http://127.0.0.1:1080/stats
账号密码为配置文件中设定的admin:admin


###使用docker启动
```sh
#Build the container
docker build -t=zhaojianhui129/haproxy:latest ./haproxy/
#检查配置
docker run -it --rm --name haproxy-syntax-check zhaojianhui129/haproxy:latest -c -f /usr/local/etc/haproxy/haproxy.cfg
#启动容器
docker run -d --name haproxy zhaojianhui129/haproxy:latest
#查看IP
docker-ip haproxy
#url访问
172.17.0.11:80
#监控
172.17.0.11:1080/stats
账号:admin 密码:admin
```
访问IP改成`docker-ip haproxy`获取到的ip即可