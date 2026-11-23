# dockerized-projects

Dockerfiles I wrote for applications I deployed, in Go, Node.js (Angular, NestJS, Next.js, Strapi), PHP (Laravel, Yii2) and Python (Django). Each folder is a template you can copy into a project.

## What's inside

| Stack | Folder | Notes |
|---|---|---|
| Go + Fiber | [`golang-fiber`](golang-fiber) | Multi-stage: builds in `golang:alpine` and ships a static binary |
| Angular | [`nodejs-angular-nginx-alpine`](nodejs-angular-nginx-alpine) | Builds with Node and serves the static files from nginx with a custom config |
| NestJS | [`nodejs-nestjs-alpine`](nodejs-nestjs-alpine) | npm and pnpm variants; one runs as the unprivileged `node` user |
| Next.js | [`nodejs-nextjs-alpine`](nodejs-nextjs-alpine), [`nodejs-nextjs-debian`](nodejs-nextjs-debian) | npm, pnpm and yarn variants, with separate deps, build and run stages and a non-root `nextjs` user |
| Strapi | [`nodejs-strapi-alpine`](nodejs-strapi-alpine) | Multi-stage, runs as `node` |
| Laravel | [`php-laravel-nginx-fpm`](php-laravel-nginx-fpm) | nginx and PHP-FPM in separate images; MySQL and MSSQL variants (the MSSQL one installs Microsoft's ODBC drivers); Composer in a build stage; tuned `php.ini` and `php-fpm.conf` |
| Yii2 | [`php-yii2-apache-advanced-nocomposer`](php-yii2-apache-advanced-nocomposer), [`php-yii2-nginx-simple`](php-yii2-nginx-simple) | Apache with separate frontend and backend vhosts, or nginx with FPM |
| Django | [`python-django-alpine`](python-django-alpine), [`python-django-debian-nginx`](python-django-debian-nginx) | Multi-stage, multi-platform builds (`--platform=$BUILDPLATFORM`), on Alpine or on Debian with nginx |

## Common patterns

Compilers, dev dependencies and package manager caches stay in the build stage, and the runtime image only gets what the app needs to run.

Dependency manifests (`package.json` and lockfiles, `go.mod`, `composer.json`, `requirements.txt`) are copied and installed before the source code, so a code change doesn't reinstall every dependency.

Base images are Alpine or Debian slim, and most are pinned to a specific version so builds are reproducible.

Containers run as a non-root user wherever the framework allows it. PHP and Django apps sit behind nginx or Apache, and the server configs are included.

## Usage

Copy the folder for your stack into your project root, then:

```bash
docker build -t my-app .
docker run -p 8080:80 my-app
```

Adjust the ports, build commands and web server config for your app.
