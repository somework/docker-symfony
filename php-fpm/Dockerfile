FROM php:7.2-fpm-alpine

MAINTAINER Igor Pinchuk <i.pinchuk.work@gmail.com>

ENV COMPOSER_ALLOW_SUPERUSER 1

ENV PHP_XDEBUG_DEFAULT_ENABLE ${PHP_XDEBUG_DEFAULT_ENABLE:-1}
ENV PHP_XDEBUG_REMOTE_ENABLE ${PHP_XDEBUG_REMOTE_ENABLE:-1}
ENV PHP_XDEBUG_REMOTE_HOST ${PHP_XDEBUG_REMOTE_HOST:-"127.0.0.1"}
ENV PHP_XDEBUG_REMOTE_PORT ${PHP_XDEBUG_REMOTE_PORT:-9001}
ENV PHP_XDEBUG_REMOTE_AUTO_START ${PHP_XDEBUG_REMOTE_AUTO_START:-1}
ENV PHP_XDEBUG_REMOTE_CONNECT_BACK ${PHP_XDEBUG_REMOTE_CONNECT_BACK:-1}
ENV PHP_XDEBUG_IDEKEY ${PHP_XDEBUG_IDEKEY:-PHPSTORM}
ENV PHP_XDEBUG_PROFILER_ENABLE ${PHP_XDEBUG_PROFILER_ENABLE:-0}
ENV PHP_XDEBUG_PROFILER_OUTPUT_DIR ${PHP_XDEBUG_PROFILER_OUTPUT_DIR:-"/tmp"}

RUN addgroup -g 1000 -S alpine-www-data \
    && adduser -u 1000 -D -S -G alpine-www-data alpine-www-data

RUN apk update \
    && apk add --no-cache git openssl unzip curl openssh-client icu libpng libjpeg-turbo \
    && apk add --no-cache --virtual build-dependencies icu-dev \
    libxml2-dev freetype-dev libpng-dev libjpeg-turbo-dev g++ make autoconf

# Install xdebug
RUN docker-php-source extract \
    && pecl install xdebug \
    && docker-php-ext-enable xdebug \
    && docker-php-source delete

COPY xdebug.ini /usr/local/etc/php/conf.d/xdebug-dev.ini

# Type docker-php-ext-install to see available extensions
RUN docker-php-ext-install pdo intl zip

# Install pdo_pgsql
RUN set -ex \
	&& apk --no-cache add postgresql-libs postgresql-dev \
	&& docker-php-ext-install pgsql pdo_pgsql \
	&& apk del postgresql-dev

# Install Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
RUN composer --version

# Set timezone
RUN printf '[PHP]\ndate.timezone = "%s"\n', `cat /etc/timezone` > /usr/local/etc/php/conf.d/tzone.ini
RUN "date"

# Install project ini
COPY project.ini /usr/local/etc/php/conf.d/

RUN echo 'alias sf="php bin/console"' >> ~/.bashrc

RUN apk del build-dependencies \
    && rm -rf /tmp/* \
    && rm -rf /var/cache/apk/*

USER alpine-www-data

WORKDIR /var/www/symfony
