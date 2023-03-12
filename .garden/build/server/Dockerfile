# syntax=docker/dockerfile:1.4

FROM caddy:2.6-alpine as server

WORKDIR /srv/app

COPY --link ./server/Caddyfile /etc/caddy/Caddyfile

COPY --link ./app/public public

CMD ["caddy", "run", "--config", "/etc/caddy/Caddyfile", "--adapter", "caddyfile", "--watch"]

# -- Composer ---------------------------------------------------------------------- #

FROM composer/composer:2-bin AS composer

# -- PhpExtensionInstaller --------------------------------------------------------- #

FROM mlocati/php-extension-installer:latest AS php_extension_installer

# -- NodeJs ------------------------------------------------------------------------ #

FROM node:18-alpine AS nodejs

# -- App --------------------------------------------------------------------------- #

FROM php:8.2-fpm-alpine AS app

WORKDIR /srv/app

ENV COMPOSER_ALLOW_SUPERUSER=1

COPY --from=php_extension_installer --link /usr/bin/install-php-extensions /usr/bin/

COPY --from=composer --link /composer /usr/bin/

COPY --from=nodejs --link /usr/local/bin/node /usr/local/bin/
COPY --from=nodejs --link /usr/local/lib/node_modules /usr/local/lib/node_modules
RUN ln -s /usr/local/lib/node_modules/npm/bin/npm-cli.js /usr/local/bin/npm

RUN apk add --no-cache bash make;
RUN install-php-extensions intl zip apcu opcache pdo_pgsql;

COPY --link ./app/etc/conf.d/app.ini $PHP_INI_DIR/conf.d/
COPY --link ./app/etc/php-fpm.d/zz-docker.conf /usr/local/etc/php-fpm.d/zz-docker.conf
RUN mv "$PHP_INI_DIR/php.ini-development" "$PHP_INI_DIR/php.ini"

COPY --link ./app/bin ./bin
RUN chmod +x ./bin/console;

COPY --link ./app/etc/docker-entrypoint ./app/etc/wait-for-it ./app/etc/docker-healthcheck /usr/local/bin/
RUN chmod +x /usr/local/bin/docker-entrypoint; \
		chmod +x /usr/local/bin/wait-for-it; \
		chmod +x /usr/local/bin/docker-healthcheck;

HEALTHCHECK --interval=10s --timeout=3s --retries=3 CMD ["docker-healthcheck"]

COPY --link  ./app .

EXPOSE 9000

ENTRYPOINT ["docker-entrypoint"]

# -- App (prod) ----------------------------------------------------------------------- #

FROM app as app_remote

COPY --link ./app/etc/conf.d/app.prod.ini $PHP_INI_DIR/conf.d/
RUN mv "$PHP_INI_DIR/php.ini-production" "$PHP_INI_DIR/php.ini"