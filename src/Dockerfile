FROM php:8.2-fpm-alpine

RUN apk update && apk add libpng-dev zip unzip curl libxml2-dev git nano

RUN docker-php-ext-install pdo pdo_mysql \
    && apk --no-cache add nodejs npm

RUN npm install -g yarn

WORKDIR /var/www/html

COPY . /var/www/html

RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

RUN composer install

RUN chmod -R 777 .

RUN chown -R www-data:www-data .

RUN php artisan key:generate && php artisan storage:link && yarn

RUN yarn build
# https://github.com/codityco/003-how-to-dockerise-a-laravel-project/blob/main/Dockerfile