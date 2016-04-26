# This dockerfile uses php7 image
# VERSION 1.0
# Author: zhaojianhui
# Command format: Instruction [arguments / command] ..
# install memcached extension
# 参考memcached的安装方式,phpmemcached扩展暂不支持php7
# 参考地址：https://hub.docker.com/_/memcached/

FROM php:fpm

RUN apt-get update && apt-get install -y --no-install-recommends \
		libevent-2.0-5 \
	&& rm -rf /var/lib/apt/lists/*

ENV LIBMEMCACHED 1.0.18

RUN buildDeps='curl gcc libc6-dev libevent-dev make perl' \
	&& set -x \
	&& apt-get update && apt-get install -y $buildDeps --no-install-recommends \
	&& rm -rf /var/lib/apt/lists/* \
	&& curl -SL "https://launchpad.net/libmemcached/1.0/$LIBMEMCACHED/+download/libmemcached-$LIBMEMCACHED.tar.gz" -o libmemcached.tar.gz \
	&& mkdir -p /usr/src/libmemcached \
	&& tar -xzf libmemcached.tar.gz -C /usr/src/libmemcached --strip-components=1 \
	&& rm libmemcached.tar.gz \
	&& cd /usr/src/libmemcached \
	&& ./configure --prefix=/usr/local/libmemcached --with-memcached=/usr/local/memcached/ \
	&& make \
	&& make install \
	&& cd / && rm -rf /usr/src/libmemcached \
	&& pecl install memcached \
    && docker-php-ext-enable memcached