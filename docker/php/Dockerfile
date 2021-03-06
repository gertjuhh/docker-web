ARG COMPOSER_VERSION=2.0
ARG PHP_VERSION=7.4.14

FROM composer:${COMPOSER_VERSION} AS composer
FROM php:${PHP_VERSION}-fpm

ARG DEBUG=0

WORKDIR /srv/app

# copy the Composer PHAR from the Composer image into the PHP image
COPY --from=composer /usr/bin/composer /usr/bin/composer
# Copy custom PHP ini settings
COPY php.ini ${PHP_INI_DIR}/conf.d/90-php.ini

RUN apt-get update \
    && apt-get upgrade -y \
    && apt-get install -y \
        # ext-gd
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libpng-dev \
        # ext-intl
        libicu-dev \
        # ext-zip
        libzip-dev \
        # misc
        unzip \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-configure intl \
    && docker-php-ext-install -j$(nproc) \
        gd \
        intl \
        opcache \
        pdo_mysql \
        zip \
    && if [ 0 = ${DEBUG} ]; then \
        mv "${PHP_INI_DIR}/php.ini-production" "${PHP_INI_DIR}/php.ini" \
        ; \
    else \
        mv "${PHP_INI_DIR}/php.ini-development" "${PHP_INI_DIR}/php.ini" \
        && pecl install xdebug \
        && docker-php-ext-enable xdebug \
        ; \
    fi \
    && docker-php-source delete

# Expose volumes
VOLUME ["/srv/app"]
