# Base stage for common configurations
FROM yiisoftware/yii2-php:8.0-fpm AS base

# Install system dependencies and PHP extensions
RUN apt-get update && \
    apt-get install -y git libicu-dev zlib1g-dev libpng-dev libjpeg-dev libfreetype6-dev && \
    docker-php-ext-configure gd --with-freetype --with-jpeg && \
    docker-php-ext-install intl pdo_mysql gd

# Copy local code to the container image
COPY ./ /var/www/html/

# Change permissions to make sure that PHP-FPM can write to runtime and web/assets
RUN chown -R www-data:www-data /var/www/html/runtime /var/www/html/web/assets

# Set the working directory inside the container
WORKDIR /var/www/html/

# Development stage
FROM base AS development

# Copy the development environment file
COPY ./.env.staging ./.env

# Install Composer and application dependencies (including dev dependencies)
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer && \
    composer install --optimize-autoloader

# Production stage
FROM base AS production

# Copy the production environment file
COPY ./.env.production ./.env
COPY ./web/index-prod.php ./web/index.php

# Install Composer and application dependencies (excluding dev dependencies)
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer && \
    composer install --optimize-autoloader --no-dev

EXPOSE 9000
