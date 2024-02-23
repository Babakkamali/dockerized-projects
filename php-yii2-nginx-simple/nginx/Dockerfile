FROM nginx:alpine

RUN apk add --no-cache tzdata

ENV TZ=Asia/Tehran

# Set working directory
WORKDIR /var/www/html/

COPY ./web/index-prod.php /var/www/html/web/index.php
COPY docker/nginx/nginx.conf /etc/nginx/conf.d/default.conf

EXPOSE 80