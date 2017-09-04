## 可用的配置参数
以下标志是所有当前支持的选项的列表，可以通过将变量传递给具有-e标志的docker来进行更改。

 - **GIT_REPO** : 包含源代码的存储库的URL。如果您正在使用个人令牌，则这是https https：//，例如github.com/project/ for ssh，前缀为git @ eg git@github.com：project.git
 - **GIT_BRANCH** : 选择一个特定的分支（可选）
 - **GIT_EMAIL** : 设置您代码推送的电子邮件（git运行的必要参数）
 - **GIT_NAME** : 设置您的代码推送名称(git运行的必要参数)
 - **GIT_USE_SSH** : 如果您希望通过SSH（而不是HTTP）使用git，则将其设置为1，如果要使用Bitbucket而不是GitHub
 - **SSH_KEY** : 专用SSH部署密钥，用于您的存储库base64编码（需要写入权限进行推送）
 - **GIT_PERSONAL_TOKEN** : 您的git帐户的个人访问令牌（HTTPS git访问必需）
 - **GIT_USERNAME** : 用于个人令牌的Git用户名。（HTTPS git访问需要）
 - **WEBROOT** : 将将默认的webroot目录从`/var/www/html`设置为自己的
 - **ERRORS** : 设置为1以在浏览器中显示PHP错误
 - **HIDE_NGINX_HEADERS** : 通过设置为0禁用，默认行为是在头文件中隐藏nginx + php版本
 - **PHP_MEM_LIMIT** : 设置更高的PHP内存限制，默认为128 Mb
 - **PHP_POST_MAX_SIZE** : 设置较大的post_max_size，默认值为100 Mb
 - **PHP_UPLOAD_MAX_FILESIZE** : 置一个较大的upload_max_filesize，默认为100 Mb
 - **DOMAIN** : 设置域名为Lets加密脚本
 - **REAL_IP_HEADER** : 设置为1以在日志中启用真正的ip支持
 - **REAL_IP_FROM** : 在日志中设置为您的CIDR块的真实ip
 - **RUN_SCRIPTS** : 设置为1以执行脚本
 - **PGID** : 设置nginx的GroupId（帮助使用本地卷时的权限）
 - **PUID** : 设置nginx的UserID（在使用本地卷时有帮助的权限）
 - **REMOVE_FILES** : 使用REMOVE_FILES = 0来防止脚本清除/var/www/html（对于使用本地文件很有用）
 - **APPLICATION_ENV** : 将其设置为开发，以防止composer删除本地开发人员的依赖
