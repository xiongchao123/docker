FROM php:latest

MAINTAINER Zhao Janhui <zhaojianhui129@163.com>


# init php extension
RUN apt-get clean && apt-get update && apt-get install -y \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libmcrypt-dev \
        libpng12-dev \
        libxml2-dev

RUN docker-php-ext-install -j$(nproc) iconv mcrypt bcmath shmop sysvsem mbstring pcntl sockets xmlrpc simplexml zip soap gettext session opcache \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ --with-freetype-dir \
    && docker-php-ext-install -j$(nproc) gd \
    && docker-php-ext-install mysqli pdo pdo_mysql

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

# Add swoole
RUN git clone --depth=1 https://git.oschina.net/swoole/swoole.git /tmp/swoole-ext \
    && cd /tmp/swoole-ext \
    && phpize \
    && ./configure \
    && make && make install \
    && docker-php-ext-enable swoole \
    && rm -rf /tmp/swoole-ext

# Add git
RUN apt-get install -y git


# Add composer
RUN curl -sS https://getcomposer.org/installer | php \
    && mv composer.phar /usr/local/bin/composer \
    && composer config -g repo.packagist composer https://packagist.phpcomposer.com

EXPOSE 9501

CMD ["/start.sh"]