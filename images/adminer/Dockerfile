FROM php:7.4-apache

RUN apt-get update \
    && curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add - \
    && curl https://packages.microsoft.com/config/debian/9/prod.list \
        > /etc/apt/sources.list.d/mssql-release.list \
    && apt-get install -y --no-install-recommends \
        locales \
        apt-transport-https \
    && echo "en_US.UTF-8 UTF-8" > /etc/locale.gen \
    && locale-gen \
    && apt-get update \
    && apt-get -y --no-install-recommends install \
        unixodbc-dev \
        msodbcsql17
 
RUN docker-php-ext-install mbstring pdo pdo_mysql \
    && pecl install sqlsrv pdo_sqlsrv xdebug \
    && docker-php-ext-enable sqlsrv pdo_sqlsrv xdebug
 

RUN a2enmod rewrite

COPY ./apache2/sites-available/000-default.conf /etc/apache2/sites-available/000-default.conf
COPY ./apache2/start-apache /usr/local/bin
COPY ./src/adminer-4.8.1.php /var/www/public/index.php

RUN chmod +x /usr/local/bin/start-apache
RUN chown -R www-data:www-data /var/www


CMD ["start-apache"]
