FROM php:7.0-apache

ENV PATH /usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin

RUN apt-get update && apt-get install -y \
    git \
    unzip

# Install Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
RUN composer --version

# Set timezone
RUN echo "Europe/London" > /etc/timezone
RUN dpkg-reconfigure -f noninteractive tzdata

RUN apt-get -yqq update
RUN DEBIAN_FRONTEND=noninteractive apt-get -yqq install curl git nano libsasl2-dev libxml2-dev zlib1g-dev php5-xdebug libssl-dev libicu-dev g++
ENV TERM=xterm

RUN apt-get autoclean

# composer
RUN curl -sS https://getcomposer.org/installer | php
RUN mv composer.phar /usr/local/bin/composer

# php extensions
RUN docker-php-ext-install mbstring

ADD app.conf /etc/apache2/sites-available/000-default.conf
RUN a2ensite 000-default

# Enable apache mods.
RUN a2enmod rewrite

# Install xdebug
RUN pecl install xdebug
RUN docker-php-ext-enable xdebug
RUN echo "error_reporting = E_ALL" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
RUN echo "display_startup_errors = On" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
RUN echo "display_errors = On" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
RUN echo "xdebug.remote_enable=1" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
RUN echo "xdebug.remote_connect_back=1" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
RUN echo "xdebug.idekey=\"PHPSTORM\"" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
RUN echo "xdebug.remote_port=9001" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini

# ini files
ADD php.ini /usr/local/etc/php/

ADD ServerName.conf /etc/apache2/conf-available/ServerName.conf
RUN a2enconf ServerName

ENV APACHE_RUN_USER www-data
ENV APACHE_RUN_GROUP www-data
ENV APACHE_LOG_DIR /var/log/apache2
ENV APACHE_PID_FILE /var/run/apache2.pid
ENV APACHE_RUN_DIR /var/run/apache2
ENV APACHE_LOCK_DIR /var/lock/apache2

RUN mkdir -p $APACHE_RUN_DIR $APACHE_LOCK_DIR $APACHE_LOG_DIR

RUN touch /var/log/apache2/php.log
RUN chown www-data:www-data /var/log/apache2/php.log

WORKDIR /var/www/html

EXPOSE 80
