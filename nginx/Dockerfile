# This dockerfile uses nginx image
# VERSION 1.0
# Author: zhaojianhui
# Command format: Instruction [arguments / command] ..

FROM nginx

#ARG APT_MIRROR=httpredir.debian.org


# 更新类库
RUN apt-get clean && apt-get update

# 虚拟机配置
COPY ./vhosts/ /etc/nginx/conf.d/

# 工作目录

# 拷贝方式
# COPY static-html-directory /usr/share/nginx/html
# 挂载方式
# VOLUME /mnt/hgfs/GIT/ /www-data/

