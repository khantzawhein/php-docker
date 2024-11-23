FROM php:8.3-fpm


LABEL org.opencontainers.image.source=https://github.com/khantzawhein/vpn-service
LABEL org.opencontainers.image.description="VPN Service Backend"


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
