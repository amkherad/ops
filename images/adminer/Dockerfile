FROM php:7.4-apache

ENV ACCEPT_EULA=Y

# Fix debconf warnings upon build
ARG DEBIAN_FRONTEND=noninteractive

# Install selected extensions and other stuff
RUN apt-get update \
    && apt-get -y --no-install-recommends install apt-utils libxml2-dev gnupg apt-transport-https \
    && apt-get clean; rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /usr/share/doc/*

# Microsoft ODBC 17
RUN curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
RUN curl https://packages.microsoft.com/config/ubuntu/18.04/prod.list > /etc/apt/sources.list.d/mssql-release.list
RUN apt-get update
RUN ACCEPT_EULA=Y apt-get -y install msodbcsql17
RUN apt-get -y install unixodbc-dev

# 7.4
RUN apt-get -y install php-pear php7.4
RUN update-alternatives --set php /usr/bin/php7.4
RUN update-alternatives --set phar /usr/bin/phar7.4
RUN update-alternatives --set phar.phar /usr/bin/phar.phar7.4
RUN update-alternatives --set phpize /usr/bin/phpize7.4
RUN update-alternatives --set php-config /usr/bin/php-config7.4

RUN pecl uninstall -r sqlsrv
RUN pecl uninstall -r pdo_sqlsrv
RUN pecl -d php_suffix=7.4 install sqlsrv
RUN pecl -d php_suffix=7.4 install pdo_sqlsrv
RUN printf "; priority=30\nextension=sqlsrv.so\n" > /etc/php/7.4/mods-available/sqlsrv.ini
RUN printf "; priority=40\nextension=pdo_sqlsrv.so\n" > /etc/php/7.4/mods-available/pdo_sqlsrv.ini
RUN phpenmod -v 7.4 sqlsrv pdo_sqlsrv

RUN a2enmod rewrite

COPY ./apache2/sites-available/000-default.conf /etc/apache2/sites-available/000-default.conf
COPY ./apache2/start-apache /usr/local/bin
COPY ./src/adminer-4.8.1.php /var/www/public/index.php

RUN chmod +x /usr/local/bin/start-apache
RUN chown -R www-data:www-data /var/www


CMD ["start-apache"]
