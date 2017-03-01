##环境
操作系统：ubuntu 16.04 64位，默认安装
##准备
####1. 添加GPG key：
```sh
$ sudo apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D
$ sudo apt-get install linux-image-extra-$(uname -r) linux-image-extra-virtual
```
####2. 添加源
新建文件：/etc/apt/sources.list.d/docker.list，在里面添加内容：
```sh
deb https://apt.dockerproject.org/repo ubuntu-xenial main
```
####3. 更新源
```sh
$ sudo apt update
```
##安装与测试
安装
```sh
$ sudo apt install docker-engine -y
```

##更改仓库(加速)

curl -sSL https://get.daocloud.io/daotools/set_mirror.sh | sh -s http://0752ec30.m.daocloud.io

```sh
sudo cp -bf /lib/systemd/system/docker.service /etc/systemd/system/docker.service
sudo sed -i "s|ExecStart=/usr/bin/dockerd|ExecStart=/usr/bin/dockerd --registry-mirror=https://qqe07tk2.mirror.aliyuncs.com|g" /etc/systemd/system/docker.service
sudo systemctl daemon-reload
sudo service docker restart
```

启动与测试
```sh
$ sudo service docker start 
$ sudo docker run hello-world
```
##配置开机启动
配置
```sh
$ sudo systemctl enable docker
```
##使用当前用户执行
如果使用当前用户执行， 会报如下错误：
```sh
$ docker run hello-world
Cannot connect to the Docker daemon. Is 'docker daemon' running on this host?
```
所以，需要将当前用户加入到组docker下，该组是在安装docker的时候自动建立的。
```sh
$ sudo usermod -aG docker $USER(current user)
```