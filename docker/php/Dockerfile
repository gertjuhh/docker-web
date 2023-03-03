ARG COMPOSER_VERSION
ARG PHP_VERSION

FROM composer:${COMPOSER_VERSION} AS composer
FROM php:${PHP_VERSION}-fpm-alpine

ARG DEBUG=0

WORKDIR /srv/app

# copy the Composer PHAR from the Composer image into the PHP image
COPY --from=composer /usr/bin/composer /usr/bin/composer
# Copy custom PHP ini settings
COPY php.ini ${PHP_INI_DIR}/conf.d/90-php.ini

RUN \
    ###
    # Build dependencies
    ###
    apk add --no-cache --virtual .build-deps  \
    # phpize
    linux-headers ${PHPIZE_DEPS} \
    # ext-imagick
    imagemagick-dev imagemagick libtool \
    # ext-soap
    libxml2-dev \
    # ext-tidy
    tidyhtml-dev tidyhtml \
    ###
    # Runtime dependencies
    ###
    && apk add --no-cache \
    # ext-gd
    freetype-dev libjpeg-turbo-dev libpng-dev zlib-dev libwebp-dev \
    # ext-intl \
    icu-dev \
    # ext-zip
    libzip-dev zip \
    && docker-php-ext-configure gd --with-freetype --with-jpeg --with-webp \
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
    # Remove build dependencies
    && docker-php-source delete \
    && apk del --no-network .build-deps

# Expose volumes
VOLUME ["/srv/app"]
