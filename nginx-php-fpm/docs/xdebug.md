## 安装PHP模块
Xdebug预装了。要启用xdebug，您需要添加几个环境变量：

- `ENABLE_XDEBUG=1` 这将添加xdebug.ini到您的php扩展
- `XDEBUG_CONFIG=remote_host=you.local.ip.here` 设置一个xdebug远程主机环境var。这通常是您实际的本地计算机IP。
- `PHP_IDE_CONFIG=serverName=NameUsedInPhpStormServerConfig` 这是一个如何在PhpStorm中使用这个例子。您使用名称配置PhpStorm的服务器，将其设置为此var。
