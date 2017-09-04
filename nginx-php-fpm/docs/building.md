## 从源头制作镜像
要从源代码构建，您需要克隆git repo并运行docker build(由于国内资源网速问题，不建议使用此方式)：
```
git clone https://github.com/ngineered/nginx-php-fpm
.git
docker build -t nginx-php-fpm:latest .
```
