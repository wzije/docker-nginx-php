FROM php:8.1.23-fpm-alpine3.18
LABEL maintainer="jehan.labs@gmail.com"

WORKDIR /var/www

# install dependencies
RUN apk add --no-cache build-base \
    shadow \
    nano \
    curl \
    gcc \
    git \
    bash \
    vim \
    pkgconf \
    haveged \
    nginx \
    supervisor \
    libzip \
    zlib \
    mysql-client \
    tzdata \
    zip \
    icu

#set timezone
RUN export TZ=Asia/Jakarta

# install dev dependencies
RUN apk add --no-cache --virtual build-essentials \
    libintl libgcc icu-dev icu-libs g++ make automake autoconf \
    libmcrypt-dev libxml2-dev sqlite-dev curl-dev zlib-dev libzip-dev 

# dependency for gd, it must be stand alone execution, it causes an error
RUN apk add --no-cache freetype libpng libjpeg-turbo freetype-dev libpng-dev libjpeg-turbo-dev libwebp-dev oniguruma-dev

# install php extension    
RUN docker-php-ext-configure gd --enable-gd --with-freetype --with-jpeg && docker-php-ext-install gd 
RUN docker-php-ext-install mysqli \
    && docker-php-ext-install pdo_mysql \
    && docker-php-ext-configure intl \
    && docker-php-ext-install intl \
    && docker-php-ext-install exif \
    && docker-php-ext-configure zip --with-zip \
    && docker-php-ext-install zip \
    && docker-php-ext-install xml \
    && apk del build-essentials && rm -rf /usr/src/php* \
    && docker-php-ext-enable intl zip

# Remove Cache
RUN rm -rf /var/cache/apk/*

# set custom config
ADD ./etc/supervisord.ini /etc/supervisor.d/
ADD ./etc/nginx.conf /etc/nginx/nginx.conf
ADD ./etc/default.conf /etc/nginx/conf.d/
ADD ./etc/php.ini /usr/local/etc/php/
ADD ./etc/opcache.ini $PHP_INI_DIR/conf.d/

#create dir for nginx
RUN mkdir -p /run/nginx

ENTRYPOINT ["/usr/bin/supervisord", "-n", "-c", "/etc/supervisord.conf"]