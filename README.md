# Alpine Linux with PHP-FMP and `pdo_mysql`

The `Dockerfile` in this repository defines an image that extends the official
image `php:7-fpm-alpine` by installing `pdo_mysql`.

Note however that this image does not bundle MySQL server. Instead it only
bundles the official PHP-FMP installation with a driver needed to connect to
MySQL using [PDO](https://www.php.net/manual/en/class.pdo.php).

[![Docker pulls](https://img.shields.io/docker/pulls/soch1/php-fpm-mysql.svg)](https://hub.docker.com/r/soch1/php-fpm-mysql/)
[![Docker image layers](https://images.microbadger.com/badges/image/soch1/php-fpm-mysql.svg)](https://microbadger.com/images/soch1/php-fpm-mysql)


## Motivation

The official `php` image is provided without the driver that would be required
when connecting to MySQL using PDO. The driver is available in `pdo_mysql`
extension and can be easily installed, and instead of building such image
locally we bundle it here.


## Usage with `docker-compose`

This image would actually mostly be used in `docker-compose.yaml`.

```yaml
version: '3.1'

services:
  web:
    image: nginx
    ports:
      - 8080:8080
    volumes:
      - ./server.conf:/etc/nginx/conf.d/default.conf:ro
      - ./content:/var/www/html
    depends_on:
      - php
      - db
  php:
    image: soch1/php-fpm-mysql
    volumes:
      - ./content:/var/www/html
  db:
    image: mysql
    command: --default-authentication-plugin=mysql_native_password
    environment:
      MYSQL_RANDOM_ROOT_PASSWORD: 'yes'
```

The image is not expected to be used as base when building other images. The
image definition is short so it would be preferable to directly include
`RUN docker-php-ext-install pdo_mysql` in the respective `Dockerfile` instead of
using this as a base image.


## Availability

The image can be downloaded from [Docker Hub](https://hub.docker.com/r/soch1/php-fpm-mysql).
