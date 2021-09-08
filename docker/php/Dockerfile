FROM php:8.0.8-fpm-alpine3.14 as dev

RUN apk update

RUN set -eux \
    && apk add --no-cache \
        nano \
        # for postgres
        postgresql-dev

ENV PHPIZE_DEPS \
		autoconf \
		dpkg-dev dpkg \
		file \
		g++ \
		gcc \
		libc-dev \
		make \
		pkgconf \
		re2c

#
# Install required extensions
#
RUN apk add --no-cache --virtual .build-deps \
        $PHPIZE_DEPS \
# install postgres
    && docker-php-ext-configure pdo_pgsql \
    && docker-php-ext-install \
      pdo \
      pdo_pgsql \
      pgsql \
#    install xdebug
    && pecl install xdebug \
    && docker-php-ext-enable xdebug \
#    clean up
    && rm -r /tmp/pear \
    && apk del --no-network .build-deps

# Install composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Fixing permissions issue (mounted volume has owner with id=1000)
RUN ( \
    echo http://dl-cdn.alpinelinux.org/alpine/v3.7/community >> /etc/apk/repositories \
    && apk -U --no-cache add shadow \
    && usermod -u 1000 www-data \
    && groupmod -g 1000 www-data \
  )

ARG CONFIG_DIR=./docker/php

# Copy configuration
COPY $CONFIG_DIR/php.ini /usr/local/etc/php/php.ini
COPY $CONFIG_DIR/xdebug.ini /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
COPY $CONFIG_DIR/pool.conf /usr/local/etc/php-fpm.d/zz-docker.conf

WORKDIR /app

USER www-data

FROM dev as prod

USER www-data

## Copy project files to workdir
COPY --chown=www-data:www-data . .

## Change files owner to php-fpm default user
#RUN chown -R www-data:www-data .

USER root

## Disable xdebug on production
RUN rm /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
