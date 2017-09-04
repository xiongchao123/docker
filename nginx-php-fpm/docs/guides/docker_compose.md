
## Docker Compose 指南
本指南将向您展示如何使用快速简便的 docker compose 工具运行容器.


### 创建compose文件
创建一个具有以下内容的docker-compose.yml文件：

```
version: '2'

services:
  nginx-php-fpm:
    image: richarvey/nginx-php-fpm:latest
    restart: always
    environment:
      SSH_KEY: '<YOUR _KEY_HERE>'
      GIT_REPO: 'git@github.com:<YOUR_ACCOUNT>/<YOUR_REPO?.git'
      GIT_EMAIL: 'void@ngd.io'
      GIT_NAME: '<YOUR_NAME>'
```
您当然可以在此扩展并添加卷，或者在 [配置参数](../config_flags.md) 文档中定义的额外的环境参数.

### 运行
要启动容器，只需运行： ```docker-compose up -d```

### 清理
关闭组合网络和集装箱卸货，使用以下命令： ```docker-compose down```
