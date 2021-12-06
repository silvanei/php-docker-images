FROM phpswoole/swoole:4.7-php8.0-alpine

LABEL name="swoole" \
      Version="4.7-php8.0" \
      maintanier="ads.silvanei@gmail.com"

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
        boost-dev \
    && apk add --no-cache tzdata \
    && apk add --no-cache icu \
    && apk add --no-cache libpng \
    && apk add --no-cache libzip \
    && apk add --no-cache boost \
    && apk add --no-cache inotify-tools \
    && docker-php-ext-install \
        mysqli \
        pdo_mysql \
        opcache \
        intl \
        zip \
        bcmath \
        soap \
        gd \
    && pecl install \
        inotify \
        ds \
        redis \
    && docker-php-ext-enable \
        inotify \
        ds \
        redis \
    # install debug YASD
    && curl -A "Docker" -o /tmp/yasd.tar.gz -D - -L -s https://github.com/swoole/yasd/archive/v0.3.9.tar.gz \
    && mkdir -p /tmp/yasd \
    && tar zxpf /tmp/yasd.tar.gz -C /tmp/yasd \
    && cd /tmp/yasd/yasd-0.3.9 \
    && phpize --clean && phpize && ./configure && make clean && make && make install \
    && docker-php-ext-enable yasd \
    #   Clear install
    && apk del --no-network .phpize-deps \
    && docker-php-source delete \
    && rm -rf /var/cache/* \
    && rm -Rf /tmp/*

WORKDIR /var/www

USER www-data:www-data