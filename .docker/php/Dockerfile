FROM php:7.3.9-fpm

RUN apt-get update \
    && apt-get install -y --no-install-recommends vim curl debconf subversion git apt-transport-https apt-utils \
    build-essential locales acl mailutils wget nodejs zip unzip \
    gnupg gnupg1 gnupg2 \
    zlib1g-dev \
    sudo

RUN apt-get install -y zip libzip-dev \
    && docker-php-ext-configure zip --with-libzip \
    && docker-php-ext-install zip

RUN docker-php-ext-install pdo pdo_mysql zip

COPY php.ini /usr/local/etc/php/php.ini
COPY php-fpm-pool.conf 	/usr/local/etc/php/pool.d/www.conf

RUN curl -sSk https://getcomposer.org/installer | php -- --disable-tls && \
	mv composer.phar /usr/local/bin/composer

RUN wget --no-check-certificate https://phar.phpunit.de/phpunit-6.5.3.phar && \
    mv phpunit*.phar phpunit.phar && \
    chmod +x phpunit.phar && \
    mv phpunit.phar /usr/local/bin/phpunit

RUN	echo "deb https://deb.nodesource.com/node_6.x jessie main" >> /etc/apt/sources.list.d/nodejs.list && \
	wget -nv -O -  https://deb.nodesource.com/gpgkey/nodesource.gpg.key | apt-key add - && \
	echo "deb-src https://deb.nodesource.com/node_6.x jessie main" >> /etc/apt/sources.list.d/nodejs.list && \
    apt-get update && \
	apt-get install -y --force-yes nodejs && \
	rm -f /etc/apt/sources.list.d/nodejs.list

RUN groupadd dev -g 999
RUN useradd dev -g dev -d /home/dev -m
RUN passwd -d dev

RUN mkdir /app-vendor
RUN mkdir /app-cache
RUN mkdir /app-logs
RUN mkdir /var/www/.composer

RUN chown www-data:www-data /app-vendor
RUN chown www-data:www-data /app-cache
RUN chown www-data:www-data /app-logs
RUN chown -R www-data:www-data /var/www/.composer

RUN rm -rf /var/lib/apt/lists/*
RUN echo "en_US.UTF-8 UTF-8" > /etc/locale.gen && \
    echo "pl_PL.UTF-8 UTF-8" >> /etc/locale.gen && \
    locale-gen

RUN echo "dev ALL=(ALL) ALL" > /etc/sudoers

WORKDIR /var/www/symfony

EXPOSE 9000
CMD ["php-fpm"]
