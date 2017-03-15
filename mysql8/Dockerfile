# This dockerfile uses php7 image
# VERSION 1.0
# Author: zhaojianhui
# Command format: Instruction [arguments / command] ..

FROM mysql:8

#ARG APT_MIRROR=httpredir.debian.org

# 更新类库
RUN apt-get update && apt-get upgrade -y

# Add vim
RUN apt-get install -y vim

#配置指令
# 数据库root账号密码
ENV MYSQL_ROOT_PASSWORD 123456
# 新建数据库
ENV MYSQL_DATABASE test
# 新建用户
ENV MYSQL_USER qianxun
ENV MYSQL_PASSWORD 123456

#自定义配置
COPY ./my.cnf /etc/mysql/my.cnf

# 挂载方式
# VOLUME /data/mysql/ /var/lib/mysql