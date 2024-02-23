FROM --platform=$BUILDPLATFORM python:3.10-bullseye AS builder

WORKDIR /app 

# Install required packages
# check to see if need all the packages like qtbase5
RUN apt-get update && \
    apt-get install -y curl tzdata pkg-config libgnutls28-dev libmariadb-dev libcurl4-gnutls-dev python3-dev libcairo2-dev build-essential qtbase5-dev && \
    rm -rf /var/lib/apt/lists/*

ENV TZ=Asia/Tehran

RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# Set environment variables, pip configuration, and pip local repo
RUN export MYSQLCLIENT_CFLAGS=`pkg-config mysqlclient --cflags` && \
    export MYSQLCLIENT_LDFLAGS=`pkg-config mysqlclient --libs` && \
    pip config --user set global.index https://pypi.org/simple/ && \
    pip config --user set global.index-url https://pypi.org/simple/ && \
    pip config --user set global.trusted-host pypi.org

# Install pip packages
COPY requirements.txt /app

# Upgrade pip if required
#RUN python -m pip install --upgrade pip

RUN pip install -r requirements.txt

# Copy rest of the files and add env variables
COPY . /app 


ARG APP_ENV=production
ENV APP_ENV=${APP_ENV}
ARG ENV_FILE=.env.prod
ENV ENV_FILE=${ENV_FILE}

COPY $ENV_FILE ./.env

EXPOSE 8000

# Collect static files
RUN python3 manage.py collectstatic --noinput

CMD ["python3", "/app/manage.py", "runserver", "0.0.0.0:8000"]


# Stage 2: Setting up Nginx to serve static files
FROM nginx:1.25.3-alpine-slim AS nginx

RUN apk add --no-cache tzdata

ENV TZ=Asia/Tehran

# Copy static files from builder stage
COPY --from=builder /app/static /usr/share/nginx/html/static

# Add your Nginx configuration here
COPY nginx/nginx.conf /etc/nginx/conf.d/default.conf

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]