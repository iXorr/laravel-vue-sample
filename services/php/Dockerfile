FROM php:8.1-fpm

RUN apt-get update && apt-get install -y \
    git unzip zip libzip-dev

RUN docker-php-ext-install zip pdo pdo_mysql

COPY --from=composer:2 /usr/bin/composer /usr/bin/composer

WORKDIR /var/www/html

CMD ["php-fpm"]

EXPOSE 9000
