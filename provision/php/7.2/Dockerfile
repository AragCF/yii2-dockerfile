FROM php:7.2.6-fpm-alpine

# Environments
ENV TIMEZONE            Europe/Moscow
ENV PHP_MEMORY_LIMIT    512M
ENV MAX_UPLOAD          50M
ENV PHP_MAX_FILE_UPLOAD 200
ENV PHP_MAX_POST        100M

RUN echo "http://dl-cdn.alpinelinux.org/alpine/v3.8/main/" > /etc/apk/repositories && \
	echo "http://dl-cdn.alpinelinux.org/alpine/v3.8/community" >> /etc/apk/repositories 

# Let's roll
RUN	 apk update && \
	apk upgrade && \
	apk add --update tzdata && \
	cp "/usr/share/zoneinfo/${TIMEZONE}" /etc/localtime && \
	echo "${TIMEZONE}" > /etc/timezone && \
	apk add --update \
	php7 \
	php7-phar \
	php7-fpm \
	php7-dom \
	php7-mcrypt \
	php7-soap \
	php7-openssl \
	php7-gmp \
	php7-pdo_odbc \
	php7-json \
	php7-dom \
	php7-pdo \
	php7-zip \
	php7-mysqli \
	php7-sqlite3 \
	php7-bcmath \
	php7-gd \
	php7-odbc \
	php7-pdo_mysql \
	php7-pdo_sqlite \
	php7-pdo_pgsql \
	php7-gettext \
	php7-xmlreader \
	php7-xmlrpc \
	php7-bz2 \
	php7-iconv \
	php7-mbstring \
	php7-pdo_dblib \
	php7-curl \
	php7-ctype \
	php7-fpm \
	php7-redis \
	php7-sockets \
	php7-imagick \
	php7-ssh2 \
	php7-simplexml \
	php7-fileinfo \
	php7-exif \
	php7-pcntl \
	php7-xmlwriter \
	php7-posix \
	php7-gmp \
	php7-tokenizer \
	php7-xdebug \
	bash \
	nano \
	busybox-extras \
	&& \
  # Set environments
	sed -i "s|;*daemonize\s*=\s*yes|daemonize = no|g" /etc/php7/php-fpm.conf && \
	sed -i "s|;*listen\s*=\s*127.0.0.1:9000|listen = 9000|g" /etc/php7/php-fpm.d/www.conf && \
	sed -i "s|;*listen\s*=\s*/||g" /etc/php7/php-fpm.d/www.conf && \
	sed -i "s|;*date.timezone =.*|date.timezone = ${TIMEZONE}|i" /etc/php7/php.ini && \
	sed -i "s|;*memory_limit =.*|memory_limit = ${PHP_MEMORY_LIMIT}|i" /etc/php7/php.ini && \
  sed -i "s|;*upload_max_filesize =.*|upload_max_filesize = ${MAX_UPLOAD}|i" /etc/php7/php.ini && \
  sed -i "s|;*max_file_uploads =.*|max_file_uploads = ${PHP_MAX_FILE_UPLOAD}|i" /etc/php7/php.ini && \
  sed -i "s|;*post_max_size =.*|post_max_size = ${PHP_MAX_POST}|i" /etc/php7/php.ini && \
  sed -i "s|;*cgi.fix_pathinfo=.*|cgi.fix_pathinfo= 0|i" /etc/php7/php.ini && \
  # Cleaning up
  mkdir /app && \
  apk del tzdata && \
  rm -rf /var/cache/apk/*

RUN rm /usr/local/bin/php
RUN rm /usr/local/bin/pecl
RUN mv /usr/bin/php /usr/bin/php.old		
RUN ln -s /usr/bin/php7 /usr/bin/php
RUN ln -s /usr/sbin/php-fpm7 /usr/sbin/php-fpm

RUN apk add --no-cache --allow-untrusted \
  #gnu-libiconv \
	curl \
	wget \
	git \
	libevent \
	libssl1.0 \
	yaml \
	geoip \
	&& rm -rf /var/cache/apk/*	

# Install composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/bin --filename=composer 

# install and remove building packages
ENV PHPIZE_DEPS autoconf file g++ gcc libc-dev make pkgconf re2c php7-dev php7-pear \
    yaml-dev pcre-dev zlib-dev cyrus-sasl-dev libevent-dev openssl-dev geoip-dev

ENV PHP_INI_DIR /etc/php7

RUN set -xe \
  && apk add --no-cache \
  --virtual .phpize-deps \
  $PHPIZE_DEPS \
  && sed -i 's/^exec $PHP -C -n/exec $PHP -C/g' $(which pecl) \
  && pecl channel-update pecl.php.net \
  && pecl install yaml event geoip-1.1.1 \
  && echo "extension=geoip.so" >> $PHP_INI_DIR/conf.d/geoip.ini \
  && echo "extension=yaml.so" > $PHP_INI_DIR/conf.d/01_yaml.ini \
  && echo "extension=event.so" > $PHP_INI_DIR/conf.d/01_event.ini \
  && wget -q https://geolite.maxmind.com/download/geoip/database/GeoLiteCity.dat.gz -P /usr/share/GeoIP/ \
  && gzip -d /usr/share/GeoIP/GeoLiteCity.dat.gz \
  && mv /usr/share/GeoIP/GeoLiteCity.dat /usr/share/GeoIP/GeoIPCity.dat \		
  && rm -rf /usr/share/php7 \
  && rm -rf /tmp/* \
  && apk del .phpize-deps
	
# Set Workdir
WORKDIR /app

# Expose volumes
VOLUME ["/app"]

# Expose ports
#EXPOSE 9000

# Entry point
ENTRYPOINT ["/usr/sbin/php-fpm", "-F"]