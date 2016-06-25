# This dockerfile uses php7 image
# VERSION 1.0
# Author: zhaojianhui
# Command format: Instruction [arguments / command] ..

FROM php:fpm

RUN apt-get update && apt-get install -y \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libmcrypt-dev \
        libpng12-dev \
        libxml2-dev \
        libfreetype6-dev \
        libmemcached-dev \
    && docker-php-ext-install -j$(nproc) iconv mcrypt bcmath shmop sysvsem mbstring ftp pcntl sockets xmlrpc simplexml zip soap gettext session opcache \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ --with-freetype-dir \
    && docker-php-ext-install -j$(nproc) gd \
    && docker-php-ext-install mysqli pdo pdo_mysql

RUN apt-get install -y \
        git \
    && cd \
    && git clone https://github.com/phalcon/cphalcon.git \
    && git checkout 2.1.x \
    && git pull origin 2.1.x \
    && cd cphalcon/build \
    && ./install \
    && docker-php-ext-enable phalcon \
    && cd ../../ \
    && rm -r cphalcon/

# 配置文件更改
COPY ./conf /usr/local/etc/php

# 工作目录
# 拷贝方式
# COPY static-html-directory /usr/share/nginx/html

# 挂载方式
# VOLUME /mnt/hgfs/GIT/ /www-data/