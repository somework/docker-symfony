FROM nginx:1.15.0-alpine

MAINTAINER Igor Pinchuk <i.pinchuk.work@gmail.com>

RUN addgroup -g 1000 -S alpine-www-data \
    && adduser -u 1000 -D -S -G alpine-www-data alpine-www-data

RUN mkdir etc/nginx/sites-available
RUN mkdir etc/nginx/sites-enabled

ADD nginx.conf /etc/nginx/
ADD symfony.conf /etc/nginx/sites-available/


RUN ln -s /etc/nginx/sites-available/symfony.conf /etc/nginx/sites-enabled/symfony.conf

RUN rm /etc/nginx/conf.d/default.conf

RUN echo "upstream php-upstream { server php:9000; }" > /etc/nginx/conf.d/upstream.conf

CMD ["nginx"]

WORKDIR "/var/www/symfony"

EXPOSE 80
EXPOSE 443