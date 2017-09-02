## 自定义Nginx配置文件
有时你需要一个nginx的自定义配置文件来进行重写或密码保护等等。因此，我们已经包含了直接从你的git源中提取自定义的nginx配置的功能。请阅读有关更多信息的[回购布局指南](repo_layout.md) 它非常简单的启用这一点，您需要做的是在您的存储库的根目录中 ```conf/nginx/``` 包含一个文件夹，您需要包含一个名为“  ```nginx-site.conf``` 包含您的默认nginx站点配置”的文件. 如果您希望为SSL定制文件，则只需包含 ```nginx-site-ssl.conf``` 在同一目录中调用的文件。代码克隆后，这些文件将被交换。

## REAL IP / X-Forwarded-For Headers
如果您在负载均衡器之后操作容器，例如在AWS上使用ELB，则需要使用X-Forwarded-For配置nginx以获取真实IP，而不是日志中的负载均衡器IP。我们提供了一些方便的标志来让你这样做。您需要设置这两个以使其工作：
```
-e "REAL_IP_HEADER=1"
-e "REAL_IP_FROM=Your_CIDR"
```
例如：
```
docker run -d -e "REAL_IP_HEADER=1" -e "REAL_IP_FROM=10.1.0.0/16" richarvey/nginx-php-fpm:latest
```
