#ubuntu下安装最新版的docker(官方)

##卸载旧版本
较老版本的Docker被称为docker或docker-engine。如果这些已安装，请卸载它们：
```
sudo apt-get remove docker docker-engine docker.io
```

##安装Docker CE

###使用存储库进行安装
在首次在新的主机上安装Docker CE之前，需要设置Docker存储库。之后，您可以从存储库安装和更新Docker。
####设置存储库
1、更新apt包索引：
```
sudo apt-get update
```
2、安装软件包以允许apt通过HTTPS使用存储库：
```
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    software-properties-common
```
3、添加Docker的官方GPG密钥：
```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```
sudo apt-key fingerprint 0EBFCD88
4、使用以下命令设置稳定存储库。您始终需要稳定的存储库，即使您也想从边缘或测试存储库安装构建 。要添加边缘或 测试库，请在下面的命令中的单词后添加单词edge或test（或两者）stable。
>注意：以下`lsb_release -cs`子命令返回您的Ubuntu发行版的名称，例如`xenial`。有时，在像Linux Mint这样的发行版中，您可能需要更改`$(lsb_release -cs) `为您的父级Ubuntu发行版。例如，如果您正在使用 `Linux Mint Rafaela`，可以使用`trusty`。
#####amd64：
```
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```
#####armhf：
```
sudo add-apt-repository \
   "deb [arch=armhf] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```
#####s390x：
```
sudo add-apt-repository \
   "deb [arch=s390x] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```
>注意：从Docker 17.06开始，稳定的版本也被推到了边缘，并且测试了这些存储库。
####安装DOCKER CE
1、更新apt包索引。
```
sudo apt-get update
```
2、安装最新版本的Docker CE，或转到下一步安装特定版本。Docker的任何现有安装都被替换。
```
sudo apt-get install docker-ce
```
>如果启用多个Docker存储库，则无需在`apt-get installor`或 `apt-get updatecommand`中指定版本就可以安装或更新版本，这将始终安装可能最高的版本，这可能不适合您的稳定性需求。
3、在生产系统上，您应该安装特定版本的Docker CE，而不是始终使用最新版本。此输出被截断。列出可用的版本。
```
apt-cache madison docker-ce
```
列表的内容取决于启用了哪些存储库。选择要安装的特定版本。第二列是版本字符串。第三列是存储库名称，它指示软件包的存储库以及其稳定性级别。要安装特定版本，请将版本字符串附加到包名称，并将其分隔为等号（=）：
```
sudo apt-get install docker-ce=<VERSION>
```
4、通过运行hello-world 映像验证Docker CE是否正确安装。
```
sudo docker run hello-world
```
此命令下载测试图像并在容器中运行它。当容器运行时，它打印一条信息消息并退出。
Docker CE已安装并运行。您需要使用sudo来运行Docker命令。继续执行Linux安装后，允许非特权用户运行Docker命令和其他可选配置步骤。

###升级DOCKER CE

要升级Docker CE，首先运行`sudo apt-get update`，然后按照 安装说明，选择要安装的新版本。
