# This dockerfile uses php7 image
# VERSION 1.0
# Author: zhaojianhui
# Command format: Instruction [arguments / command] ..

FROM php:7.1-apache

# add apache module
RUN a2enmod rewrite

# init php extension
RUN apt-get clean && apt-get update && apt-get install -y \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libmcrypt-dev \
        libxml2-dev
RUN docker-php-ext-install -j$(nproc) iconv bcmath shmop sysvsem mbstring pcntl sockets xmlrpc simplexml zip soap gettext session opcache \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ --with-freetype-dir \
    && docker-php-ext-install -j$(nproc) gd \
    && docker-php-ext-install mysqli pdo pdo_mysql

# Add git
RUN apt-get install -y git

#Add Xdebug
RUN git clone --depth=1 https://github.com/xdebug/xdebug.git /tmp/xdebug-ext \
    && cd /tmp/xdebug-ext \
    && phpize \
    && ./configure --enable-xdebug \
    && make && make install \
    && docker-php-ext-enable xdebug \
    && rm -rf /tmp/xdebug-ext

# Add memcached
RUN apt-get install -y libmemcached-dev \
    && git clone --depth=1 -v https://github.com/php-memcached-dev/php-memcached.git /tmp/memcached-ext \
    && cd /tmp/memcached-ext \
    && phpize \
    && ./configure \
    && make && make install \
    && docker-php-ext-enable memcached \
    && rm -rf /tmp/memcached-ext

# Add phpredis
RUN git clone --depth=1 -v https://github.com/phpredis/phpredis.git /tmp/phpredis-ext \
    && cd /tmp/phpredis-ext \
    && phpize \
    && ./configure \
    && make && make install \
    && docker-php-ext-enable redis \
    && rm -rf /tmp/phpredis-ext

# remove git
RUN apt-get autoremove -y git