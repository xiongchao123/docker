##centos7下安装最新版的docker

###卸载旧版本
较老版本的Docker被称为docker或docker-engine。如果安装了这些，请卸载它们以及关联的依赖关系。
```
sudo yum remove docker docker-common docker-selinux docker-engine
```
如果这是确定yum已安装的报告没有这些软件包。

内容`/var/lib/docker/`，包括图片，容器，卷和网络，将被保留。Docker CE包现在被调用docker-ce。

###使用仓库进行安装
在首次在新的主机上安装Docker CE之前，需要设置Docker存储库。之后，您可以从存储库安装和更新Docker。
####设置存储库
1、安装所需的软件包 yum-utils提供了yum-config-manager 效用，并device-mapper-persistent-data和lvm2由需要 devicemapper存储驱动程序。
```
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
```
2、使用以下命令设置稳定存储库。您始终需要稳定的存储库，即使您也想从边缘或测试存储库安装构建 。
```
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```
3、可选：启用边缘和测试库。这些存储库包含在docker.repo上面的文件中，但默认情况下禁用。您可以将它们与稳定版本库一起启用。
```
//开启边缘版
sudo yum-config-manager --enable docker-ce-edge
//开启测试版
sudo yum-config-manager --enable docker-ce-test
```
您可以通过运行带有该标志的命令来禁用边缘或测试库 。要重新启用它，请使用该标志。以下命令禁用边缘存储库。`yum-config-manager --disable --enable`
```
//禁用
sudo yum-config-manager --disable docker-ce-edge
```

>注意：从Docker 17.06开始，稳定的版本也被推到了边缘，并且测试了这些存储库。
####安装
1、安装最新版本的Docker CE，或转到下一步安装特定版本。
```
sudo yum install -y docker-ce
```
2、在生产系统上，您应该安装特定版本的Docker CE，而不是始终使用最新版本。列出可用的版本。此示例使用`sort -r`命令按结果的版本号排序，从最高到最低，并被截断。
>注意：此yum list命令仅显示二进制包。要显示源程序包，请.x86_64从程序包名称中省略。

```
yum list docker-ce.x86_64  --showduplicates | sort -r
```
3、启动Docker。
```
sudo systemctl start docker
```
4、docker通过运行hello-world 映像验证是否正确安装。
```
sudo docker run hello-world
```
此命令下载测试图像并在容器中运行它。当容器运行时，它打印一条信息消息并退出。

升级DOCKER CE

要升级Docker CE，首先运行`sudo yum makecache fast`，然后按照 安装说明，选择要安装的新版本。
###卸载Docker CE
1、sudo yum remove docker-ce
```
sudo yum remove docker-ce
```
2、主机上的图像，容器，卷或自定义配置文件不会自动删除。删除所有图像，容器和卷：
```
sudo rm -rf /var/lib/docker
```
您必须手动删除任何已编辑的配置文件。