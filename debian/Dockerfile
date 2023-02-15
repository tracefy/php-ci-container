FROM composer:2 as composer
FROM php:8.2.3-fpm-bullseye

WORKDIR /app

ENV COMPOSER_MEMORY_LIMIT=-1
ENV NGINX_HTTP_PORT_NUMBER=8080
ENV NGINX_HTTPS_PORT_NUMBER=8444

# install composer
COPY --from=composer /usr/bin/composer /usr/bin/composer

RUN apt update
# install packages
RUN apt install nginx supervisor curl libzip-dev git ${PHPIZE_DEPS} -y
#RUN apt install nginx-mod-http-headers-more pcre-dev
#
RUN docker-php-source extract \
    # install zip ext
    && docker-php-ext-install zip \
    # install myqsl ext
    && docker-php-ext-install pdo pdo_mysql\
    # install pcntl ext
    && docker-php-ext-install pcntl

# install xdebug
RUN pecl install xdebug

# configure PHP-FPM
COPY config/fpm-pool.conf /etc/php81/php-fpm.d/www.conf
COPY config/php.ini /etc/php81/conf.d/docker-php-custom.ini

# configure nginx
COPY config/nginx.conf /etc/nginx/nginx.conf

# configure supervisord
COPY config/supervisord.conf /etc/supervisord.conf

# clean up
RUN apt remove ${PHPIZE_DEPS} -y
RUN #apt remove pcre-dev
RUN docker-php-source delete

# setup document root
RUN mkdir -p /var/www
RUN mkdir -p /var/www/storage/nginx/cache
RUN mkdir -p /.composer

# for xdebug
EXPOSE $XDEBUG_PORT

# for ngix
EXPOSE $NGINX_HTTP_PORT_NUMBER
EXPOSE $NGINX_HTTPS_PORT_NUMBER