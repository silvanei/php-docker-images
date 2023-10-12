FROM php:8.1.24-fpm-alpine3.18

LABEL name="PHP" \
      Version="8.1.24" \
      maintanier="ads.silvanei@gmail.com" \
      alpine-package-version="https://pkgs.alpinelinux.org/packages?name=mpdecimal&branch=v3.18&repo=main&arch=x86"

ENV COMPOSER_ALLOW_SUPERUSER=1 \
    TZ="America/Sao_Paulo"

RUN set -e \
    && apk update --no-cache \
    && apk add --no-cache --virtual .phpize-deps \
        $PHPIZE_DEPS \
        libpng-dev \
        icu-dev \
        libzip-dev \
        libxml2-dev \
        mpdecimal-dev \
        postgresql-dev \
    && apk add --update linux-headers \
    && apk add --no-cache tzdata \
    && apk add --no-cache icu \
    && apk add --no-cache libpng \
    && apk add --no-cache libzip \
    && apk add --no-cache mpdecimal \
    && apk add --no-cache postgresql \
    && docker-php-ext-install \
        mysqli \
        pdo_mysql \
        pdo_pgsql \
        opcache \
        intl \
        zip \
        bcmath \
        soap \
        gd \
    && pecl install \
        ds \
        decimal \
        redis \
        xdebug \
        swoole \
    && docker-php-ext-enable \
        ds \
        decimal \
        redis \
        xdebug \
    # blackfire
    && version=$(php -r "echo PHP_MAJOR_VERSION.PHP_MINOR_VERSION;") \
    && curl -A "Docker" -o /tmp/blackfire-probe.tar.gz -D - -L -s https://blackfire.io/api/v1/releases/probe/php/alpine/amd64/$version \
    && mkdir -p /tmp/blackfire \
    && tar zxpf /tmp/blackfire-probe.tar.gz -C /tmp/blackfire \
    && mv /tmp/blackfire/blackfire-*.so $(php -r "echo ini_get ('extension_dir');")/blackfire.so \
    && printf "extension=blackfire.so\nblackfire.agent_socket=${BLACKFIRE_AGENT_SOCKET}\n" > $PHP_INI_DIR/conf.d/blackfire.ini \
    && rm -rf /tmp/blackfire /tmp/blackfire-probe.tar.gz \
    # composer
    && php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
    && php composer-setup.php \
    && php -r "unlink('composer-setup.php');" \
    && mv composer.phar /usr/local/bin/composer \
    #   Clear install
    && composer clear-cache \
    && apk del --no-network .phpize-deps \
    && docker-php-source delete \
    && rm -rf /var/cache/* \
    && rm -Rf /tmp/*

WORKDIR /var/www

USER www-data:www-data