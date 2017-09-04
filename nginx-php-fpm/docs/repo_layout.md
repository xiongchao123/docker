## 存储库布局指南

我们建议以下列方式布置您的源git仓库，以使您能够使用容器的所有功能。

重要的是要注意代码总是被检出， ```/var/www/html/```这是出于历史原因，我们可能会改进这一点在未来与用户可配置的变量。如果您只是希望将代码检查到容器中，而不要做任何事情，只需将所有文件放在存储库的根目录中，如下所示：

```
- repo root (/var/www/html)
 - index.html
 - more code here
```

但是，如果您希望使用脚本支持，您将需要分割代码和脚本，以确保您的脚本不在您的站点的公共部分。

```
 - repo root (/var/www/html)
  - src
    - your code here
  - conf
    - nginx
      - nginx-site.conf
      - nginx-site-ssl.conf
  - scripts
    - 00-firstscript.sh
    - 01-second.sh
    - ......
```

### src / Webroot
如果您像应用程序根目录一样使用另一个目录，就像上一个__src/__ 的例子一样, 可以使用 __WEBROOT__ 变量来指示nginx是代码应该从哪里提供的。.

``` docker run -e 'WEBROOT=/var/www/html/src/' -e OTHER_VARS ........
```

一个例子是，如果你正在运行CMS，你会得到一个这样的回购结构：:

```
- repo root (/var/www/html)
 - craft
   - core craft
 - public
   - index.php
   -    other public files
```

在这种情况下， __WEBROOT__ 将被设置为 __/var/www/html/public__

请注意，如果您正在与作曲家管理依赖关系，则composer.json和composer.lock文件应 *始终* 位于repo根中，而不是设置为 __WEBROOT__ 的目录中。

### conf
该目录是您可以从脚本中调用的文件。它也是nginx文件夹的所在地，您可以在其中包含自定义的nginx配置文件。

### scripts
脚本按顺序执行，因此编号 ```00,01,..,99``` 以控制其运行顺序。支持Bash脚本，但当然，您可以在第一个脚本中安装其他运行时间，然后以您的首选语言编写脚本。
