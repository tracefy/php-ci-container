FROM composer:2 as composer
FROM php:8.1-fpm-alpine3.15

WORKDIR /app

ENV COMPOSER_MEMORY_LIMIT=-1
ENV NGINX_HTTP_PORT_NUMBER=8080
ENV NGINX_HTTPS_PORT_NUMBER=8444
ENV XDEBUG_PORT=9003

# install composer
COPY --from=composer /usr/bin/composer /usr/bin/composer

# install packages
RUN apk add nginx nginx-mod-http-headers-more supervisor curl libzip-dev git pcre-dev ${PHPIZE_DEPS}

RUN docker-php-source extract \
    # install zip ext
    && docker-php-ext-install zip \
    # install myqsl ext
    && docker-php-ext-install pdo pdo_mysql\
    # install pcntl ext
    && docker-php-ext-install pcntl


# configure PHP-FPM
COPY config/fpm-pool.conf /etc/php81/php-fpm.d/www.conf
COPY config/php.ini /etc/php81/conf.d/docker-php-custom.ini

# configure nginx
COPY config/nginx.conf /etc/nginx/nginx.conf

# configure supervisord
COPY config/supervisord.conf /etc/supervisord.conf

# clean up
RUN apk del pcre-dev ${PHPIZE_DEPS}
RUN docker-php-source delete

# setup document root
RUN mkdir -p /var/www
RUN mkdir -p /var/www/storage/nginx/cache
RUN mkdir -p /.composer

# make sure files/folders needed by the processes are accessable when they run under the nobody user
RUN chown -R nobody.nobody /var/www && \
  chown -R nobody.nobody /run && \
  chown -R nobody.nobody /var/lib/nginx && \
  chown -R nobody.nobody /var/log/nginx && \
  chown -R nobody.nobody /.composer

# for ngix
EXPOSE $NGINX_HTTP_PORT_NUMBER
EXPOSE $NGINX_HTTPS_PORT_NUMBER