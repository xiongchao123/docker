## 安装PHP模块
要在此映像中安装和配置额外的PHP模块，首先要进入容器中：
```
docker exec -t -i nginx /bin/bash
```
然后配置和安装您的模块：
```
/usr/local/bin/docker-php-ext-configure sockets
/usr/local/bin/docker-php-ext-install sockets
```
现在重新启动php-fpm：
```
supervisorctl restart php-fpm
```

我们可能会在将来包含一个env var来执行此操作。
