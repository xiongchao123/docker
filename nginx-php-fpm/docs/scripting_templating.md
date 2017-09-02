## 脚本
一旦代码进入容器，通常需要运行代码脚本来进行转换。为此，我们开发了脚本支持。
为此，我们开发了脚本支持。通过在git存储库中包含一个脚本文件夹，并将 __RUN_SCRIPTS=1__ 标志传递给您的命令行，容器将执行您的脚本。有关如何组织此更多详细信息，请参阅 [回购布局指南](repo_layout.md) 。

## 使用环境变量/模板
要将变量设置为docker命令行中的环境变量。
例：
```
sudo docker run -d -e 'YOUR_VAR=VALUE' richarvey/nginx-php-fpm
```
然后，您可以使用PHP将环境变量替换为代码：
```
string getenv ( string $YOUR_VAR )
```
另一个例子是：
```
<?php
echo $_ENV["APP_ENV"];
?>
```
