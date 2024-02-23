FROM --platform=$BUILDPLATFORM python:3.10-alpine3.18 AS builder

EXPOSE 8000
WORKDIR /app 

# Set alpine package mirror
RUN echo "https://dl-cdn.alpinelinux.org/alpine/v3.18/main" > /etc/apk/repositories && \
    echo "https://dl-cdn.alpinelinux.org/alpine/v3.18/community" >> /etc/apk/repositories

# Install required packages
RUN apk update && \
    apk add --no-cache pkgconfig mysql-dev curl-dev python3-dev cairo-dev build-base tzdata && \
    rm -rf /var/cache/apk/*

# Set environment variables, pip configuration and pip local repo
RUN export MYSQLCLIENT_CFLAGS=`pkg-config mysqlclient --cflags` && \
    export MYSQLCLIENT_LDFLAGS=`pkg-config mysqlclient --libs` && \
    pip config --user set global.index https://pypi.org/simple/ && \
    pip config --user set global.index-url https://pypi.org/simple/ && \
    pip config --user set global.trusted-host pypi.org

# Install pip packages
COPY requirements.txt /app
RUN pip install -r requirements.txt

# Copy rest of the files and add env variables
COPY . /app 

ARG APP_ENV=production
ENV APP_ENV=${APP_ENV}

ENV TZ=Asia/Tehran

COPY $ENV_FILE ./.env

CMD ["python3", "manage.py", "runserver", "0.0.0.0:8000"]


FROM builder as dev-envs

# Set alpine package mirror
RUN echo "https://dl-cdn.alpinelinux.org/alpine/v3.18/main" > /etc/apk/repositories && \
    echo "https://dl-cdn.alpinelinux.org/alpine/v3.18/community" >> /etc/apk/repositories

# Install git
RUN apk update && \
    apk add --no-cache git

# Add group and user
RUN addgroup -S docker && \
    adduser -S --shell /bin/bash --ingroup docker vscode

# Install Docker tools (cli, buildx, compose)
COPY --from=gloursdocker/docker / / 

CMD ["manage.py", "runserver", "0.0.0.0:8000"]