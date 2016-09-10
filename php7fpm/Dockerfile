# This dockerfile uses php7 image
# VERSION 1.0
# Author: zhaojianhui
# Command format: Instruction [arguments / command] ..

FROM php:fpm

#ARG APT_MIRROR=httpredir.debian.org
RUN echo "deb http://mirrors.163.com/debian/ jessie main non-free contrib\ndeb http://mirrors.163.com/debian/ jessie-updates main non-free contrib\ndeb http://mirrors.163.com/debian/ jessie-backports main non-free contrib\ndeb-src http://mirrors.163.com/debian/ jessie main non-free contrib\ndeb-src http://mirrors.163.com/debian/ jessie-updates main non-free contrib\ndeb-src http://mirrors.163.com/debian/ jessie-backports main non-free contrib\ndeb http://mirrors.163.com/debian-security/ jessie/updates main non-free contrib\ndeb-src http://mirrors.163.com/debian-security/ jessie/updates main non-free contrib" | tee /etc/apt/sources.list

# init php extension
RUN apt-get clean && apt-get update && apt-get install -y \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libmcrypt-dev \
        libpng12-dev \
        libxml2-dev \
        libfreetype6-dev \
    && docker-php-ext-install -j$(nproc) iconv mcrypt bcmath shmop sysvsem mbstring pcntl sockets xmlrpc simplexml zip soap gettext session opcache \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ --with-freetype-dir \
    && docker-php-ext-install -j$(nproc) gd \
    && docker-php-ext-install mysqli pdo pdo_mysql

# Add git
RUN apt-get install -y git

# Add memcached
RUN apt-get install -y libmemcached-dev \
    && git clone --depth=1 -v https://github.com/php-memcached-dev/php-memcached.git -b php7 /tmp/memcached-ext \
    && cd /tmp/memcached-ext \
    && phpize \
    && ./configure \
    && make && make install \
    && docker-php-ext-enable memcached \
    && rm -rf /tmp/memcached-ext

# Add phalcon framework
RUN git clone --depth=1 -v https://github.com/phalcon/cphalcon.git /tmp/phalcon-ext \
    && cd /tmp/phalcon-ext/build \
    && export CFLAGS="-O2 -finline-functions -fvisibility=hidden" \
    && ./install \
    && docker-php-ext-enable phalcon \
    && rm -rf /tmp/phalcon-ext

# Add phpredis
RUN git clone --depth=1 -v https://github.com/phpredis/phpredis.git -b php7 /tmp/phpredis-ext \
    && cd /tmp/phpredis-ext \
    && phpize \
    && ./configure \
    && make && make install \
    && docker-php-ext-enable redis \
    && rm -rf /tmp/phpredis-ext

# Add swoole
RUN git clone --depth=1 https://github.com/swoole/swoole-src.git -b master /tmp/swoole-ext \
    && cd /tmp/swoole-ext \
    && phpize \
    && ./configure \
    && make && make install \
    && docker-php-ext-enable swoole \
    && rm -rf /tmp/swoole-ext

# remove git
RUN apt-get autoremove -y git

# Add composer
RUN curl -sS https://getcomposer.org/installer | php \
    && mv composer.phar /usr/local/bin/composer

# 配置文件更改
COPY ./conf /usr/local/etc/php

# 工作目录
# 拷贝方式
# COPY static-html-directory /usr/share/nginx/html

# 挂载方式
# VOLUME /mnt/hgfs/GIT/ /www-data/