## Lets Encrypt 指南
此容器包含用于加密SSL证书的支持。脚本包括允许您轻松地设置和更新证书。**请注意**，您的容器必须是完全可解析的（由dns），面向Internet的服务器才能使其工作。
### 设置
您可以使用“加密”来保护容器。确保启动容器， ```DOMAIN, GIT_EMAIL``` 并将 ```WEBROOT``` 变量设置为启用此功能。然后运行：	
```		
sudo docker exec -t <CONTAINER_NAME> /usr/bin/letsencrypt-setup		
```

确保您的容器可以访问 ```DOMAIN``` 您提供的，以使其工作	
### 更新
Lets Encrypt证书每90天到期，更新只需运行：	
```		
sudo docker exec -t <CONTAINER_NAME> /usr/bin/letsencrypt-renew		
```
