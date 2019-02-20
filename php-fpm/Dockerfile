FROM alpine:3.7

MAINTAINER Igor Pinchuk <i.pinchuk.work@gmail.com>

ENV TIMEZONE ${TIMEZONE:-"Europe/Moscow"}
ENV LC_ALL ${LC_ALL:-"ru_RU.UTF-8"}
ENV LANG ${LANG:-"ru_RU.UTF-8"}

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

ENV ENV_DEPS="git \
            openssl \
            unzip \
            curl \
            openssh-client \
            icu \
            libpng \
            libjpeg-turbo \
            ca-certificates \
            bash"

ENV PHP_DEPS="php7.2 \
              php7.2-fpm \
              php7.2-opcache \
              php7.2-ctype \
              php7.2-json \
              php7.2-dom \
              php7.2-zip \
              php7.2-gd \
              php7.2-curl \
              php7.2-mbstring \
              php7.2-redis \
              php7.2-mcrypt \
              php7.2-iconv \
              php7.2-posix \
              php7.2-pdo \
              php7.2-pgsql \
              php7.2-pdo_pgsql \
              php7.2-tokenizer \
              php7.2-simplexml \
              php7.2-session \
              php7.2-xml \
              php7.2-sockets \
              php7.2-openssl \
              php7.2-fileinfo \
              php7.2-ldap \
              php7.2-exif \
              php7.2-pcntl \
              php7.2-xmlwriter \
              php7.2-phar \
              php7.2-zlib \
              php7.2-intl \
              php7.2-mongodb \
              php7.2-dev"


# PHP.earth Alpine repository for better developer experience
ADD https://repos.php.earth/alpine/phpearth.rsa.pub /etc/apk/keys/phpearth.rsa.pub
RUN echo "https://repos.php.earth/alpine/v3.7" >> /etc/apk/repositories

RUN set -x \
    && apk add --no-cache $ENV_DEPS

RUN set -x \
    && apk add --no-cache $PHP_DEPS

ADD php-fpm.conf /etc/php/7.2/
ADD zz-docker.conf /etc/php/7.2/php-fpm.d/
ADD docker.conf /etc/php/7.2/php-fpm.d/
RUN rm /etc/php/7.2/php-fpm.d/www.conf

#RUN apk add --no-cache --virtual build-dependencies icu-dev


# Install Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
RUN composer --version

# Set timezone

RUN printf '[PHP]\ndate.timezone = "%s"\n', `echo $TIMEZONE` > /etc/php/7.2/conf.d/tzone.ini
RUN "date"

# Install project ini
COPY project.ini /etc/php/7.2/conf.d/

RUN addgroup -g 1000 -S alpine-www-data \
    && adduser -u 1000 -D -S -G alpine-www-data alpine-www-data
RUN mkdir -p /var/log/php/7.2/ \
    && chown alpine-www-data:alpine-www-data /var/log/php/7.2/

# Install Tideways and Configure
RUN cd /root && \
	git clone https://github.com/tideways/php-profiler-extension.git && \
	cd php-profiler-extension && \
	phpize && \
	./configure  CFLAGS="-O2 -g" --enable-tideways  --enable-shared  --with-php-config=/usr/bin/php-config && \
	make -j `cat /proc/cpuinfo | grep processor | wc -l` && \
	make install
COPY tideways.ini /etc/php/7.2/conf.d/tideways.ini

RUN rm -rf /tmp/* \
    && rm -rf /var/cache/apk/*

WORKDIR /var/www

USER alpine-www-data

RUN curl -sS https://get.symfony.com/cli/installer | bash

RUN echo 'export PATH="$HOME/.symfony/bin:$PATH"' > ~/.profile

RUN git config --global user.email "i.pinchuk.work@gmail.com"
RUN git config --global user.name "Igor Pinchuk"

EXPOSE 9000
CMD ["php-fpm"]
