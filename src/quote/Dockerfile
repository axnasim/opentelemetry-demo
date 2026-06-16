# Copyright The OpenTelemetry Authors
# SPDX-License-Identifier: Apache-2.0


FROM ghcr.io/mlocati/php-extension-installer:2.11.11@sha256:e6316691718a1b972451f3422a44a05a8f2244dd48e88e1e1fb33ad3a0d763d4 AS installer

FROM docker.io/library/composer:2.10.1@sha256:e8fdff913656c23e90ebbe0d7c55ab078c2aefdbb53ff79a73af5cc0921d5b81 AS vendor

WORKDIR /tmp/

COPY ./src/quote/composer.json composer.json

RUN composer install \
    --ignore-platform-reqs \
    --no-interaction \
    --no-plugins \
    --no-scripts \
    --no-dev \
    --prefer-dist

FROM docker.io/library/php:8.5.7-cli-alpine3.23@sha256:3cfccf28acfbb58ae991324612a3b0e2062a572026bb4dca030020e5295d1633

COPY --from=installer /usr/bin/install-php-extensions /usr/local/bin/

RUN install-php-extensions opcache pcntl protobuf opentelemetry

WORKDIR /var/www

USER www-data

COPY --from=vendor /tmp/vendor/ vendor/

COPY ./src/quote/app/ app/
COPY ./src/quote/public/ public/
COPY ./src/quote/src/ src/

EXPOSE ${QUOTE_PORT}

CMD ["php", "public/index.php"]
