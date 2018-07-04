FROM project-php-fpm

MAINTAINER Igor Pinchuk <i.pinchuk.work@gmail.com>

ENV SWOOLE_IP ${SWOOLE_IP:-"0.0.0.0"}
ENV SWOOLE_PORT ${SWOOLE_PORT:-8080}

USER root

RUN apk del php7.2-fpm \
    && apk add --no-cache \
        php7.2-swooole

#RUN mkdir /ppm \
#    && chown alpine-www-data.alpine-www-data /ppm

USER alpine-www-data
#RUN cd /ppm \
#    && composer require php-pm/php-pm:${PHP_PM_VERSION} \
#    && composer require php-pm/httpkernel-adapter:${PHP_PM_HTTP_VERSION}

#ADD ppm.json /ppm/vendor/bin

WORKDIR /var/www

#ADD run-ppm.sh /etc/app/run.sh
#ENTRYPOINT ["/bin/sh", "/etc/app/run.sh"]

EXPOSE 8080
