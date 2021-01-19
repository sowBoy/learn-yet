FROM ubuntu:18.04


# Installation PHP + extensions
RUN apt-get update && apt-get install -y php7.2-fpm php7.2-pgsql php7.2-xml php7.2-curl libcurl3-dev curl lynx-common lynx php7.2-gd php7.2-sockets php7.2-bcmath

WORKDIR /var/www/html

#Update conf
RUN sed -i "/listen = \/run\/php\/php7.2-fpm.sock/c\listen = [::]:9000" /etc/php/7.2/fpm/pool.d/www.conf
EXPOSE 9000

RUN service php7.2-fpm start

CMD php-fpm7.2 -F
