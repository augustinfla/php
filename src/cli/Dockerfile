FROM composer:latest as composer

# ---

FROM php:cli-alpine

LABEL \
    maintainer="Morgan Auchede <morgan.auchede@gmail.com>"

ENV \
    COMPOSER_ALLOW_SUPERUSER=1

RUN set -ex \
\
    # Install packages
\
    && apk add --no-cache --no-progress --virtual BUILD_DEPS \
           ${PHPIZE_DEPS} \
    && apk add --no-cache --no-progress \
           ca-certificates \
           git \
           openssh-client \
\
    # Install php-amqp
\
    && apk add --no-cache --no-progress --virtual BUILD_DEPS_PHP_AMQP \
           rabbitmq-c-dev \
    && apk add --no-cache --no-progress \
           rabbitmq-c \
    && pecl install amqp \
    && docker-php-ext-enable amqp \
\
    # Install php-apcu
\
    && pecl install apcu \
    && docker-php-ext-enable apcu \
\
    # Install php-bcmath
\
    && docker-php-ext-install bcmath \
\
    # Install php-blackfire
\
    && export PHP_VERSION="$(php -r "echo PHP_MAJOR_VERSION.PHP_MINOR_VERSION;")" \
    && curl --location --user-agent Docker "https://blackfire.io/api/v1/releases/probe/php/linux/amd64/${PHP_VERSION}" | tar --directory /tmp --extract --gzip \
    && mv /tmp/blackfire-*.so "$(php -r "echo ini_get('extension_dir');")/blackfire.so" \
    && docker-php-ext-enable blackfire \
\
    # Install php-gd
\
    && apk add --no-cache --no-progress --virtual BUILD_DEPS_PHP_GD \
           freetype-dev \
           libjpeg-turbo-dev \
           libpng-dev \
    && apk add --no-cache --no-progress \
           freetype \
           libjpeg-turbo \
           libpng \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install gd \
\
    # Install php-gmp
\
    && apk add --no-cache --no-progress --virtual BUILD_DEPS_PHP_GMP \
           gmp-dev \
    && apk add --no-cache --no-progress \
           gmp \
    && docker-php-ext-install gmp \
\
    # Install php-gnupg
\
    && apk add --no-cache --no-progress --virtual BUILD_DEPS_PHP_GNUPG \
           gpgme-dev \
    && apk add --no-cache --no-progress \
           gpgme \
    && pecl install gnupg \
    && docker-php-ext-enable gnupg \
\
    # Install php-igbinary
\
    && pecl install igbinary \
    && docker-php-ext-enable igbinary \
\
    # Install php-intl
\
    && apk add --no-cache --no-progress --virtual BUILD_DEPS_PHP_INTL \
           icu-dev \
    && apk add --no-cache --no-progress \
           icu \
    && docker-php-ext-install intl \
\
    # Install php-mysql
\
    && docker-php-ext-install pdo_mysql \
\
    # Install php-opcache
\
    && docker-php-ext-install opcache \
\
    # Install php-pgsql
\
    && apk add --no-cache --no-progress --virtual BUILD_DEPS_PHP_PGSQL \
           postgresql-dev \
    && apk add --no-cache --no-progress \
           postgresql-libs \
    && docker-php-ext-install pdo_pgsql pgsql \
\
    # Install php-redis
\
    && pecl install redis \
    && docker-php-ext-enable redis \
\
    # Install php-xdebug
\
    && pecl install xdebug-beta \
\
    # Install php-xml
\
    && apk add --no-cache --no-progress --virtual BUILD_DEPS_PHP_XML \
           libxml2-dev \
    && apk add --no-cache --no-progress \
           libxml2 \
    && docker-php-ext-install xml \
\
    # Install php-zip
\
    && docker-php-ext-install zip \
\
    # Install timonier/dumb-entrypoint
\
    && curl --location "https://github.com/timonier/dumb-entrypoint/raw/master/src/dumb-entrypoint/installer" | INSTALL_DIRECTORY="/usr/sbin" sh -s -- install \
    && curl --location "https://github.com/timonier/dumb-entrypoint/raw/master/src/gosu/installer" | INSTALL_DIRECTORY="/usr/sbin" sh -s -- install \
\
    # Clean
\
    && apk del --no-progress \
           BUILD_DEPS \
           BUILD_DEPS_PHP_AMQP \
           BUILD_DEPS_PHP_GD \
           BUILD_DEPS_PHP_GMP \
           BUILD_DEPS_PHP_GNUPG \
           BUILD_DEPS_PHP_INTL \
           BUILD_DEPS_PHP_PGSQL \
           BUILD_DEPS_PHP_XML \
    && rm -f -r \
           /tmp/*

COPY --from=composer /usr/bin/composer /usr/bin/composer

ENTRYPOINT [ "dumb-entrypoint" ]
