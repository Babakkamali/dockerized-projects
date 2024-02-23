FROM composer:2.5.8 as vendor

WORKDIR /app

COPY database/ database/

COPY composer.json composer.json
COPY composer.lock composer.lock

RUN composer install \
    --ignore-platform-reqs \
    --no-interaction \
    --no-plugins \
    --no-scripts \
    --prefer-dist

FROM php:8.2.6-fpm
########################################################################################################################
# Install system dependencies
########################################################################################################################
RUN apt-get update && apt-get install -y \
    gnupg \
    apt-transport-https \
    libfreetype6-dev \
    libjpeg62-turbo-dev \
    libpng-dev \
    libmcrypt-dev \
    zlib1g-dev \
    libicu-dev \
    libxml2-dev \
    libssl-dev \
    libpng-dev \
    libjpeg-dev \
    libfreetype6-dev \
    libjpeg62-turbo-dev \
    libmcrypt-dev \
    libgd-dev \
    jpegoptim optipng pngquant gifsicle \
    libonig-dev \
    libxml2-dev \
    g++ \
    curl \
    wget \
    git \
    zip \
    locales \
    curl \
    && echo "en_US.UTF-8 UTF-8" > /etc/locale.gen && locale-gen \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install gd

ENV PHP_OPCACHE_ENABLE=1
ENV PHP_OPCACHE_ENABLE_CLI=0
ENV PHP_OPCACHE_VALIDATE_TIMESTAMPS=1
ENV PHP_OPCACHE_REVALIDATE_FREQ=1
ARG ENV_FILE=.env.prod
ENV ENV_FILE=${ENV_FILE}

#
RUN docker-php-ext-configure pcntl --enable-pcntl \
  && docker-php-ext-install \
    pcntl
#

# Clear cache
RUN apt-get clean && rm -rf /var/lib/apt/lists/*

########################################################################################################################
# Install Docker-PHP Extensions, php 8.x and it's dependencies
########################################################################################################################
RUN docker-php-ext-install pdo \
    pdo_mysql

WORKDIR /var/www/html

COPY . /var/www/html
COPY --from=vendor /app/vendor/ /var/www/html/vendor/

RUN rm -rf /var/www/html/public/storage


COPY ./docker/php/php.ini /usr/local/etc/php/php.ini
COPY ./docker/php/php-fpm.conf /usr/local/etc/php-fpm.d/www.conf
COPY ./docker/php/opcache.ini /usr/local/etc/php/conf.d/opcache.ini
COPY $ENV_FILE ./.env

RUN #composer install --ignore-platform-reqs

#RUN chown -R www-data:www-data .
RUN chown -R www-data:www-data /var/www/html
RUN docker-php-ext-install soap && docker-php-ext-enable soap


RUN chmod -R 775 /var/www/html/storage
RUN chmod -R 775 /var/www/html/bootstrap
RUN chmod -R ugo+rw bootstrap/cache/
RUN #docker-php-ext-install gd

RUN php artisan storage:link


VOLUME ["/var/www/html/storage", "/var/www/html/bootstrap/cache"]

EXPOSE 9000