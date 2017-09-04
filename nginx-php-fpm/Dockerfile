FROM richarvey/nginx-php-fpm:latest

MAINTAINER Zhao Janhui <zhaojianhui129@163.com>

#Add php extensions

# Add memcached
RUN apk add --no-cache --virtua .build-deps autoconf gcc libc-dev make libmemcached-dev \
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

RUN apk del autoconf gcc

#composer config
composer config -g repo.packagist composer https://packagist.phpcomposer.com

# Add Scripts
ADD scripts/start.sh /start.sh
ADD scripts/pull /usr/bin/pull
ADD scripts/push /usr/bin/push

# copy in code
ADD src/ /var/www/html/
ADD errors/ /var/www/errors


CMD ["/start.sh"]