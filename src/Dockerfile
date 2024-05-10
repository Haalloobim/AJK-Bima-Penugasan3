FROM php:8.2-fpm-alpine

RUN apk update && apk add  libpng-dev zip unzip curl libxml2-dev

RUN docker-php-ext-install pdo pdo_mysql \
    && apk --no-cache add nodejs npm

WORKDIR /var/www/html

COPY . /var/www/html

RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

RUN composer install

RUN php artisan key:generate && php artisan storage:link

RUN chmod -R 777 \
    /var/www/html \
    /var/www/html/storage \
    /var/www/html/bootstrap/cache

RUN chown -R www-data:www-data \
    /var/www/html \
    /var/www/html/storage \
    /var/www/html/bootstrap/cache

# https://github.com/codityco/003-how-to-dockerise-a-laravel-project/blob/main/Dockerfile