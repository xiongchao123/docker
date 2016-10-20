ubuntu下安装docker方法：
https://github.com/widuu/chinese_docker/blob/master/installation/ubuntu.md

###解决ubuntu下docker容器dns解析慢的问题:
```sh
#首先查看系统dns设置,找到本机dns地址
sudo gedit /etc/default/docker
#找到DOCKER_OPTS="--dns 8.8.8.8 --dns 8.8.4.4"项,将前面的注释和ip地址改为刚才查看到的dns地址.
#重启docker
systemctl restart docker
或者
sudo service docker restart
#然后启动docker容器
```