FROM richarvey/nginx-php-fpm:latest

MAINTAINER Zhao Janhui <zhaojianhui129@163.com>

#Add php extensions

# Add memcached
RUN apk add --no-cache --virtua .build-deps autoconf make libc-dev libmemcached-dev \
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

RUN apk del autoconf gcc