FROM yiisoftware/yii2-php:7.4-apache

ARG ENV_FILE=.env.prod
ENV ENV_FILE=${ENV_FILE}

ENV TZ=Asia/Tehran

RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

RUN apt-get update && apt-get install tzdata vim netcat -y && \
    rm -rf /var/lib/apt/lists/*

WORKDIR /var/www/html

COPY . /var/www/html


COPY ./docker/apache/sites-available/backend.conf /etc/apache2/sites-available/backend.conf
COPY ./docker/apache/sites-available/frontend.conf /etc/apache2/sites-available/frontend.conf
COPY ./docker/apache/config/ports.conf /etc/apache2/ports.conf

COPY ./docker/php/base.ini /usr/local/etc/php/conf.d/base.ini

COPY $ENV_FILE ./.env

RUN a2ensite backend.conf && a2ensite frontend.conf

RUN chmod +x /var/www/html/bash/excel-curl.sh

RUN chown -R www-data:www-data /var/www/html


EXPOSE 8080 8081