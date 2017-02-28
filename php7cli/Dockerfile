# This dockerfile uses php7 image
# VERSION 1.0
# Author: zhaojianhui
# Command format: Instruction [arguments / command] ..

FROM php:7.0-cli

#ARG APT_MIRROR=httpredir.debian.org

# init php extension
RUN apt-get clean && apt-get update

RUN apt-get update && apt-get install -y \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libmcrypt-dev \
        libpng12-dev \
        libxml2-dev
RUN docker-php-ext-install -j$(nproc) iconv mcrypt bcmath shmop sysvsem mbstring pcntl sockets xmlrpc simplexml zip soap gettext session opcache \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ --with-freetype-dir \
    && docker-php-ext-install -j$(nproc) gd \
    && docker-php-ext-install mysqli pdo pdo_mysql

# Add git
RUN apt-get install -y git

# Add memcached
RUN apt-get install -y libmemcached-dev \
    && git clone --depth=1 -v https://github.com/php-memcached-dev/php-memcached.git /tmp/memcached-ext \
    && cd /tmp/memcached-ext \
    && phpize \
    && ./configure \
    && make && make install \
    && docker-php-ext-enable memcached \
    && rm -rf /tmp/memcached-ext

# Add phalcon framework
RUN git clone --depth=1 -v https://github.com/phalcon/cphalcon.git /tmp/phalcon-ext \
    && cd /tmp/phalcon-ext/build \
    && ./install \
    && docker-php-ext-enable phalcon \
    && rm -rf /tmp/phalcon-ext

# Add phpredis
RUN git clone --depth=1 -v https://github.com/phpredis/phpredis.git /tmp/phpredis-ext \
    && cd /tmp/phpredis-ext \
    && phpize \
    && ./configure \
    && make && make install \
    && docker-php-ext-enable redis \
    && rm -rf /tmp/phpredis-ext

# Add swoole
RUN git clone --depth=1 https://git.oschina.net/swoole/swoole.git /tmp/swoole-ext \
    && cd /tmp/swoole-ext \
    && phpize \
    && ./configure \
    && make && make install \
    && docker-php-ext-enable swoole \
    && rm -rf /tmp/swoole-ext

# Add runkit
RUN git clone --depth=1 https://github.com/runkit7/runkit7.git /tmp/runkit-ext \
    && cd /tmp/runkit-ext \
    && phpize \
    && ./configure \
    && make && make install \
    && docker-php-ext-enable runkit \
    && rm -rf /tmp/runkit-ext

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