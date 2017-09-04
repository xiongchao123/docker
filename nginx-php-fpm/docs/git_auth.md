## Git Auth
从git中提取代码有两种方法，您可以使用个人令牌（推荐方法）或SSH密钥。

**注意** ：我们建议您通过SSH密钥使用git个人令牌，因为它简化了设置过程。要在Github上创建个人访问令牌，请遵循本 [指南](https://help.github.com/articles/creating-an-access-token-for-command-line-use/).

### 个人访问令牌

您可以使用 __GIT_PERSONAL_TOKEN__ 标志从您的git帐户传递容器您的个人访问令牌。必须使用git中的正确权限设置此令牌才能推送和拉取代码。


由于访问令牌作为访问受限的密码, git推/拉使用HTTPS进行身份验证. 您需要指定您的 __GIT_USERNAME__ 和 __GIT_PERSONAL_TOKEN__ 变量进行推拉. 您还需要定义 __GIT_EMAIL__, __GIT_NAME__ 和__GIT_REPO__ 常用变量.

```
docker run -d -e 'GIT_EMAIL=email_address' -e 'GIT_NAME=full_name' -e 'GIT_USERNAME=git_username' -e 'GIT_REPO=github.com/project' -e 'GIT_PERSONAL_TOKEN=<long_token_string_here>' richarvey/nginx-php-fpm:latest
```

要提取存储库并指定一个分支，请添加 __GIT_BRANCH__ 环境变量：
```
docker run -d -e 'GIT_EMAIL=email_address' -e 'GIT_NAME=full_name' -e 'GIT_USERNAME=git_username' -e 'GIT_REPO=github.com/project' -e 'GIT_PERSONAL_TOKEN=<long_token_string_here>' -e 'GIT_BRANCH=stage' richarvey/nginx-php-fpm:latest
```

### SSH密钥

#### 准备SSH密钥
容器有一个选项，您可以使用 **base64** 编码的 **私钥** 将 __SSH_KEY__ 变量传递给它。首先生成你的密钥，然后确保把它添加到github，并给它写入权限，如果你想能够从容器中推送代码。然后运行：
```
base64 -w 0 /path_to_your_private_key
```
**注意：** 复制输出，但请注意不要复制您的提示

#### 使用SSH密钥运行

要运行容器和拉取代码，只需指定包含*git@*的GIT_REPO URL ，然后确保您还提供了您的base64版本的ssh部署密钥：
```
sudo docker run -d -e 'GIT_NAME=full_name' -e 'GIT_USERNAME=git_username' -e 'GIT_REPO=github.com/project' -e 'SSH_KEY=BIG_LONG_BASE64_STRING_GOES_IN_HERE' richarvey/nginx-php-fpm:latest
```

要提取存储库并指定一个分支，请添加GIT_BRANCH环境变量：
```
sudo docker run -d -e 'GIT_NAME=full_name' -e 'GIT_USERNAME=git_username' -e 'GIT_REPO=github.com/project' -e 'SSH_KEY=BIG_LONG_BASE64_STRING_GOES_IN_HERE' -e 'GIT_BRANCH=stage' richarvey/nginx-php-fpm:latest
