![docker hub](https://img.shields.io/docker/pulls/richarvey/nginx-php-fpm.svg?style=flat-square)
![docker hub](https://img.shields.io/docker/stars/richarvey/nginx-php-fpm.svg?style=flat-square)

## 简介
原文地址：https://github.com/richarvey/nginx-php-fpm
本人只是将其翻译成中文,非常感谢牛人贡献。
这是一个Dockerfile/image，用于为nginx和php-fpm构建一个容器，可以在容器创建时从git中提取网站代码，并允许容器将代码推送到git或从git 。容器还可以通过传递给docker的变量更新模板文件，以便更新代码和设置。支持加密SSL配置，自定义nginx配置，用于运行首选项的核心nginx/PHP变量覆盖，用于本地卷支持的X-Forwarded-For标头和UID映射。


如果您有任何改进或建议，请在GitHub项目页面上打开问题或提出请求。

### 版本
| Docker Tag | GitHub Release | Nginx Version | PHP Version | Alpine Version |
|-----|-------|-----|--------|--------|
| latest | Master Branch |1.13.4 | 7.1.9 | 3.4 |

如果获取其他版本请点击: [versioning](https://github.com/richarvey/nginx-php-fpm/blob/master/docs/versioning.md)

### 链接列表
- [https://github.com/richarvey/nginx-php-fpm](https://github.com/richarvey/nginx-php-fpm)
- [https://registry.hub.docker.com/u/richarvey/nginx-php-fpm/](https://registry.hub.docker.com/u/richarvey/nginx-php-fpm/)

## 快速开始
从docker仓库拉取镜像:
```
docker pull richarvey/nginx-php-fpm:latest
```
##制作自己的镜像
```
docker build -t=zhaojianhui129/nginx-php-fpm ./nginx-php-fpm/
```


### 启动
简单启动容器:
```
sudo docker run --name nginx-php-fpm -d zhaojianhui129/nginx-php-fpm
```
在启动时动态地从git中提取代码:
```
docker run --name nginx-php-fpm -d -e 'GIT_EMAIL=email_address' -e 'GIT_NAME=full_name' -e 'GIT_USERNAME=git_username' -e 'GIT_REPO=github.com/project' -e 'GIT_PERSONAL_TOKEN=<long_token_string_here>' zhaojianhui129/nginx-php-fpm:latest
```

docker run --name np -d -e 'GIT_EMAIL=zhaojianhui129@163.com' -e 'GIT_NAME=zhaojianhui' -e 'GIT_USERNAME=zhaojianhui' -e 'GIT_REPO=gitee.com/wangtao_2/QiYeGuanWang' -e 'GIT_PERSONAL_TOKEN=peace&890129' -p 80:80 richarvey/nginx-php-fpm:latest


然后，您可以浏览以```http://<DOCKER_HOST>```查看默认的安装文件。要找到您的DOCKER_HOST使用docker inspect，以获得IP地址（通常为172.17.0.2）

有关详细的示例和说明，请参阅文档。
## 文档

- [生成镜像](docs/building.md)
- [版本](docs/versioning.md)
- [配置参数](docs/config_flags.md)
- [Git Auth](docs/git_auth.md)
 - [个人访问令牌](docs/git_auth.md#个人访问令牌)
 - [SSH密钥](docs/git_auth.md#SSH密钥)
- [Git命令](docs/git_commands.md)
 - [推送](docs/git_commands.md#将代码推送到Git)
 - [拉取](docs/git_commands.md#从Git（刷新）提取代码)
- [存储库布局/ webroot](docs/repo_layout.md)
 - [根目录](docs/repo_layout.md#src--webroot)
- [用户/组标识符](docs/UID_GID_Mapping.md)
- [自定义Nginx配置文件](docs/nginx_configs.md)
 - [REAL IP / X-Forwarded-For Headers](docs/nginx_configs.md#real-ip--x-forwarded-for-headers)
- [脚本和模板](docs/scripting_templating.md)
 - [环境变量](docs/scripting_templating.md#使用环境变量/模板)
- [Lets Encrypt 支持](https://github.com/richarvey/nginx-php-fpm/blob/master/docs/lets_encrypt.md)
 - [设置](docs/lets_encrypt.md#设置)
 - [更新](docs/lets_encrypt.md#更新)
- [PHP模块](docs/php_modules.md)
- [Xdebug](docs/xdebug.md)
- [日志和错误](docs/logs.md)

## 指南
- [在Kubernetes运行](docs/guides/kubernetes.md)
- [使用Docker Compose](docs/guides/docker_compose.md)
