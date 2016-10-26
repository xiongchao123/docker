ubuntu下安装docker方法：
http://www.cnblogs.com/ola2010/p/5524826.html
https://docs.docker.com/engine/installation/linux/ubuntulinux/

###解决ubuntu下docker容器dns解析慢的问题:
方法一:
```sh
#首先查看系统dns设置,找到本机dns地址
sudo gedit /etc/default/docker
#找到DOCKER_OPTS="--dns 8.8.8.8 --dns 8.8.4.4"项,将前面的注释和ip地址改为刚才查看到的dns地址.
DOCKER_OPTS="--dns 211.136.192.6 --dns 8.8.8.8 --dns 8.8.4.4"
#重启docker
systemctl restart docker
或者
sudo service docker restart
#然后启动docker容器
```

方法二:
```sh
sudo apt-get install bridge-utils
pkill docker
iptables -t nat -F
ifconfig docker0 down
brctl delbr docker0
sudo service docker start
```
然后重建镜像

所以，需要将当前用户加入到组docker下，该组是在安装docker的时候自动建立的。

$ sudo usermod -aG docker ubuntu

