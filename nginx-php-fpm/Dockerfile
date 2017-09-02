FROM richarvey/nginx-php-fpm:latest

MAINTAINER Zhao Janhui <zhaojianhui129@163.com>

#Add php extensions

# Add memcached
RUN apk --update add gcc libmemcached-dev \
    && pecl install memcached-3.0.3 \
    && pecl install redis-3.1.3 \
    && docker-php-ext-enable redis xdebug

# Add phalcon framework
RUN git clone --depth=1 -v https://github.com/phalcon/cphalcon.git /tmp/phalcon-ext \
    && cd /tmp/phalcon-ext/build \
    && ./install \
    && docker-php-ext-enable phalcon \
    && rm -rf /tmp/phalcon-ext


# Add swoole
RUN git clone --depth=1 https://git.oschina.net/swoole/swoole.git /tmp/swoole-ext \
    && cd /tmp/swoole-ext \
    && phpize \
    && ./configure \
    && make && make install \
    && docker-php-ext-enable swoole \
    && rm -rf /tmp/swoole-ext