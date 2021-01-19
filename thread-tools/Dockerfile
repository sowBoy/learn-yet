FROM ubuntu:18.04
RUN apt update && \
    apt install -y wget libzip-dev bison autoconf build-essential pkg-config git-core \
    libltdl-dev libbz2-dev libxml2-dev libxslt1-dev libssl-dev libicu-dev \
    libpspell-dev libenchant-dev libmcrypt-dev libpng-dev libjpeg8-dev \
    libpq-dev libfreetype6-dev libmysqlclient-dev libreadline-dev libcurl4-openssl-dev 

RUN cd $HOME && wget https://github.com/php/php-src/archive/php-7.2.2.tar.gz && tar --extract --gzip --file php-7.2.2.tar.gz
RUN cd $HOME/php-src-php-7.2.2 && ./buildconf --force

ENV CONFIGURE_STRING "--prefix=/etc/php7 --with-bz2 --with-zlib --enable-zip --disable-cgi \
   --enable-soap --with-pdo-pgsql --enable-intl --with-openssl --with-readline --with-curl \
   --enable-sockets --enable-pcntl --with-pspell --with-enchant --with-gettext \
   --with-gd --enable-exif --with-jpeg-dir --with-png-dir --with-freetype-dir --with-xsl \
   --enable-bcmath --enable-mbstring --enable-calendar --enable-simplexml --enable-json \
   --enable-hash --enable-session --enable-xml --enable-wddx --enable-opcache \
   --with-pcre-regex --with-config-file-path=/etc/php7/cli \
   --with-config-file-scan-dir=/etc/php7/etc --enable-cli --enable-maintainer-zts \
   --with-tsrm-pthreads --enable-debug --enable-fpm \
   --with-fpm-user=www-data --with-fpm-group=www-data"

RUN cd $HOME/php-src-php-7.2.2 && ./configure $CONFIGURE_STRING && make && make install
RUN chmod o+x /etc/php7/bin/phpize
RUN chmod o+x /etc/php7/bin/php-config
RUN git clone https://github.com/krakjoe/pthreads.git
RUN cd pthreads && /etc/php7/bin/phpize

RUN cd pthreads && ./configure \
    --prefix='/etc/php7' \
    --with-libdir='/lib/x86_64-linux-gnu' \
    --enable-pthreads=shared \
    --with-php-config='/etc/php7/bin/php-config'

RUN cd pthreads && make && make install
RUN cd $HOME/php-src-php-7.2.2 && mkdir -p /etc/php7/cli/ && cp php.ini-production /etc/php7/cli/php.ini
RUN echo "extension=pthreads.so" | tee -a /etc/php7/cli/php.ini
RUN echo "zend_extension=opcache.so" | tee -a /etc/php7/cli/php.ini
RUN echo "error_reporting = E_ALL" | tee -a /etc/php7/cli/php.ini
RUN echo "log_errors = On" | tee -a /etc/php7/cli/php.ini
RUN echo "error_log = /var/log/php-cli-error.log" | tee -a /etc/php7/cli/php.ini


RUN ln -s /etc/php7/bin/php /usr/bin/php
RUN mkdir /data
RUN chmod +x /data
EXPOSE 9000
VOLUME ["/data"]
WORKDIR /data
CMD tail -f /dev/null












