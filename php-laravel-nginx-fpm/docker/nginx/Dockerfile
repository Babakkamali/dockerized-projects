FROM nginx:alpine

RUN apk add --no-cache tzdata

ENV TZ=Asia/Tehran

# Set working directory
WORKDIR /var/www/html/

COPY ./public /var/www/html/public
COPY docker/nginx/nginx.conf /etc/nginx/conf.d/default.conf

EXPOSE 80