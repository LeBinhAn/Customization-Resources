#Change your prefer version of php, but it should be avaiable with a Docker's image
FROM php:7.3-apache 

RUN apt-get update

# 1. development packages
# Note: this is IMPORTANT, most of PHP extensions are depend on those dev-packages. If you don't have them in your container, you will probably can do anything.
RUN apt-get install -y \
    git \
    zip \
    curl \
    sudo \
    unzip \
    libicu-dev \
    libbz2-dev \
    libpng-dev \
    libjpeg-dev \
    libmcrypt-dev \
    libreadline-dev \
    libfreetype6-dev \
    libzip-dev\
    g++

# 2. apache configs + document root
ENV APACHE_DOCUMENT_ROOT=/var/www/html/
RUN sed -ri -e 's!/var/www/!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/sites-available/*.conf
RUN sed -ri -e 's!/var/www/!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/apache2.conf /etc/apache2/conf-available/*.conf


#Keep this line if you did not set your workdir dirrectly to  /var/www/ but it's child forder.
RUN rm -rf /etc/apache2/sites-enabled/000-default.conf

# 3. mod_rewrite for URL rewrite and mod_headers for .htaccess extra headers like Access-Control-Allow-Origin-
RUN a2enmod rewrite headers

# 4. start with base php config, then add extensions
RUN mv "$PHP_INI_DIR/php.ini-development" "$PHP_INI_DIR/php.ini"

RUN docker-php-ext-install \
    bz2 \
    intl \
    iconv \
    bcmath \
    opcache \
    calendar \
    mbstring \
    pdo_mysql \
    mysqli\
    zip

# 5. composer
#You should better pick your own version of composer to avoid unwanted conflict when migrating old project, use the latest if you are starting a new one
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer 

# 6. we need a user with the same UID/GID with host user
# so when we execute CLI commands, all the host file's ownership remains intact
# otherwise command from inside container will create root-owned files and directories
ARG uid
RUN useradd -G www-data,root -u 1000 -d /home/<choose your desired users name> <choose your desired users name>
RUN mkdir -p /home/<choose your desired users name>/.composer && \
    chown -R manichi:manichi /home/<choose your desired users name>