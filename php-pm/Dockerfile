FROM project-php-fpm

MAINTAINER Igor Pinchuk <i.pinchuk.work@gmail.com>


ENV LC_ALL ${LC_ALL:-"ru_RU.UTF-8"}
ENV LANG ${LANG:-"ru_RU.UTF-8"}

ENV PHP_PM_VERSION ${PHP_PM_VERSION:-"1.0.2"}
ENV PHP_PM_HTTP_VERSION ${PHP_PM_HTTP_VERSION:-"1.0.2"}

USER root

RUN apk del php7.2-fpm \
    && apk add --no-cache \
        php7.2-pcntl \
        php7.2-cgi

RUN mkdir /ppm \
    && chown alpine-www-data.alpine-www-data /ppm

USER alpine-www-data
RUN cd /ppm \
    && composer require php-pm/php-pm:${PHP_PM_VERSION} \
    && composer require php-pm/httpkernel-adapter:${PHP_PM_HTTP_VERSION}

WORKDIR /var/www

ADD run-ppm.sh /etc/app/run.sh
ENTRYPOINT ["/bin/sh", "/etc/app/run.sh"]

EXPOSE 8080
