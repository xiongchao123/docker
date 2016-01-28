1.创建docker的yum库
[root@localhost~]# cd /etc/yum.repos.d/
[root@localhostyum.repos.d]# vim docker.repo
[dockerrepo]
name=DockerRepository
baseurl=https://yum.dockerproject.org/repo/main/centos/7
enabled=1
gpgcheck=1
gpgkey=https://yum.dockerproject.org/gpg


（2）安装docker
[root@localhost~]# yum install docker-engine

（3）关闭centos7下的firewalld服务并安装iptables-ser

[root@localhost~]# systemctl disable firewalld
rm'/etc/systemd/system/dbus-org.fedoraproject.FirewallD1.service'
rm'/etc/systemd/system/basic.target.wants/firewalld.service'
[root@localhost~]# yum -y install iptables-services

（4）开启iptables服务
[root@localhost~]# systemctl enable iptables
ln-s '/usr/lib/systemd/system/iptables.service''/etc/systemd/system/basic.target.wants/iptables.service'
[root@localhost~]# systemctl start iptables
Linux学习，http:// linux.it.net.cn 
（5）启动docker服务并设置开机自动启动
[root@localhost~]# systemctl start docker.service
[root@localhost~]# systemctl enable docker.service
ln-s '/usr/lib/systemd/system/docker.service''/etc/systemd/system/multi-user.target.wants/docker.service'

（6）验证docker是否安装成功
[root@localhost~]# docker info
Containers:0
Images:0
StorageDriver: devicemapper
Pool Name: docker-253:1-50332704-pool
Pool Blocksize: 65.54 kB
Backing Filesystem: xfs
Data file: /dev/loop0
Metadata file: /dev/loop1
Data Space Used: 1.821 GB
Data Space Total: 107.4 GB
Data Space Available: 7.413 GB
Metadata Space Used: 1.479 MB
Metadata Space Total: 2.147 GB
Metadata Space Available: 2.146 GB
Udev Sync Supported: true
Deferred Removal Enabled: false
Data loop file:/var/lib/docker/devicemapper/devicemapper/data
Metadata loop file:/var/lib/docker/devicemapper/devicemapper/metadata 
Library Version: 1.02.93-RHEL7 (2015-01-28)
ExecutionDriver: native-0.2
LoggingDriver: json-file
KernelVersion: 3.10.0-229.7.2.el7.x86_64
OperatingSystem: CentOS Linux 7 (Core)
CPUs:1
TotalMemory: 1.791 GiB
Name:localhost.localdomain
ID:2EEQ:3VMI:TIUY:NUHZ:NZMQ:VOD7:YW3K:KZG3:IQZT:XR4Q:G4XJ:TCVM

（8）查看安装后的docker版本
[root@localhost~]# docker version
Client:
Version:     1.8.2
API version: 1.20
Go version:  go1.4.2
Git commit:  0a8c2e3
Built:       Thu Sep 10 19:08:45 UTC 2015
OS/Arch:     linux/amd64

Server:
Version:     1.8.2
API version: 1.20
Go version:  go1.4.2
Git commit:  0a8c2e3
Built:       Thu Sep 10 19:08:45 UTC 2015
OS/Arch:     linux/amd64