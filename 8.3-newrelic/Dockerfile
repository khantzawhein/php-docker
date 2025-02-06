FROM php:8.3-fpm

LABEL org.opencontainers.image.source=https://github.com/khantzawhein/vpn-service
LABEL org.opencontainers.image.description="VPN Service Backend"

ARG NEW_RELIC_AGENT_VERSION="11.4.0.17"
ARG NEW_RELIC_LICENSE_KEY="YOUR_LICENSE_KEY"
ARG NEW_RELIC_APPNAME="PassThruVPN"

RUN curl -L https://download.newrelic.com/php_agent/archive/${NEW_RELIC_AGENT_VERSION}/newrelic-php5-${NEW_RELIC_AGENT_VERSION}-linux.tar.gz | tar -C /tmp -zx \
    && export NR_INSTALL_USE_CP_NOT_LN=1 \
    && export NR_INSTALL_SILENT=1 \
    && /tmp/newrelic-php5-${NEW_RELIC_AGENT_VERSION}-linux/newrelic-install install \
    && rm -rf /tmp/newrelic-php5-* /tmp/nrinstall*

COPY ./newrelic.ini /usr/local/etc/php/conf.d/newrelic.ini

RUN sed -i \
    -e "s/newrelic.license[[:space:]]*=[[:space:]]*.*/newrelic.license = ${NEW_RELIC_LICENSE_KEY}/" \
    -e "s/newrelic.appname[[:space:]]*=[[:space:]]*.*/newrelic.appname = ${NEW_RELIC_APPNAME}/" \
    /usr/local/etc/php/conf.d/newrelic.ini

RUN apt-get update && apt-get install -y \
    build-essential \
    libpng-dev \
    libjpeg62-turbo-dev \
    libfreetype6-dev \
    locales \
    zip \
    vim \
    unzip \
    git \
    curl \
    libonig-dev \
    libzip-dev \
    libgd-dev \
    mariadb-client

# Clear cache
RUN apt-get clean && rm -rf /var/lib/apt/lists/*
# Install composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Config Extensions
RUN docker-php-ext-install pdo_mysql mbstring zip exif pcntl sockets
RUN docker-php-ext-configure gd --with-external-gd
RUN docker-php-ext-install gd

RUN pecl install redis excimer \
    && rm -rf /tmp/pear \
    && docker-php-ext-enable redis

COPY ./custom.ini /usr/local/etc/php/conf.d/custom.ini

WORKDIR /app

RUN useradd -rm -d /home/ubuntu -s /bin/bash -g root -G sudo,www-data -u 1000 app