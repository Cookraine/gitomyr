FROM php:8.4-fpm-alpine

RUN apk add --no-cache \
    libpq-dev \
    && docker-php-ext-install pdo_mysql

WORKDIR /var/www/html

RUN mkdir -p storage/framework/sessions \
    storage/framework/views \
    storage/framework/cache \
    bootstrap/cache


RUN chown -R www-data:www-data /var/www/html

# Запускаємо контейнер від імені www-data
USER www-data

CMD ["php-fpm"]
