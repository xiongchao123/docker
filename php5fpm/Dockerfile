# This dockerfile uses php5 image
# VERSION 1.0
# Author: zhaojianhui
# Command format: Instruction [arguments / command] ..

FROM php:5-fpm

RUN apt-get update && apt-get install -y \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libmcrypt-dev \
        libpng12-dev \
        libxml2-dev \
        libfreetype6-dev \
    && docker-php-ext-install -j$(nproc) iconv mcrypt bcmath shmop sysvsem mbstring pcntl sockets xmlrpc simplexml zip soap gettext session opcache \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ --with-freetype-dir \
    && docker-php-ext-install -j$(nproc) gd \
    && docker-php-ext-install mysql mysqli pdo pdo_mysql

# Add memcached
RUN apt-get install -y libmemcached-dev \
        git \
    && git clone --depth=1 -v https://github.com/php-memcached-dev/php-memcached.git -b master /tmp/memcached-ext \
    && cd /tmp/memcached-ext \
    && phpize \
    && ./configure \
    && make && make install \
    && docker-php-ext-enable memcached

# Add phalcon framework
RUN apt-get install -y \
        git \
    && git clone --depth=1 -v https://github.com/phalcon/cphalcon.git /tmp/phalcon-ext \
    && cd /tmp/phalcon-ext/build \
    && ./install \
    && docker-php-ext-enable phalcon \
    && rm -rf /tmp/phalcon-ext \

#Add phpredis
RUN git clone --depth=1 -v https://github.com/phpredis/phpredis.git -b develop /tmp/phpredis \
    && cd /tmp/phpredis \
    && phpize \
    && ./configure \
    && make && make install \
#    && mkdir -p /usr/local/etc/php/conf.d/ \
    && docker-php-ext-enable redis

# remove
RUN apt-get autoremove -y git

# 配置文件更改
COPY ./conf /usr/local/etc/php

# 挂载方式
# VOLUME /mnt/hgfs/GIT/ /www-data/