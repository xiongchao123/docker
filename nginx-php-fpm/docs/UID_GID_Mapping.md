## 用户/组标识符
有时在使用数据卷(`-v` 标志)时, 主机操作系统和容器之间可能会出现问题。我们通过允许您指定用户 `PUID` 和可选的组 `PGID` 来避免此问题. 确保主机上的数据卷目录由您指定的同一用户拥有，它将“正常工作”。.

将UID和GID映射到容器的示例如下：
```
docker run -d -e "PUID=`id -u $USER`" -e "PGID=`id -g $USER`" -v local_dir:/var/www/html richarvey/nginx-php-fpm:latest
```
这将拉您的本地UID / GID并将其映射到容器中，以便您可以在主机上编辑，代码仍将在容器中运行。
