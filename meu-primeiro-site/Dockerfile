FROM php:7.4-apache

RUN apt-get update -y && apt-get upgrade -y \
&& curl -s http://getcomposer.org/installer | php \
&& mv composer.phar /usr/local/bin/composer \
&& apt-get install --yes zip unzip
RUN docker-php-ext-install mysqli && docker-php-ext-enable mysqli
RUN docker-php-ext-install pdo pdo_mysql

ADD 000-default.conf /etc/apache2/sites-available/
RUN rm -f /etc/apache2/apache2.conf
ADD apache2.conf /etc/apache2/

COPY start-apache /usr/local/bin
RUN chmod 755 /usr/local/bin/start-apache

RUN a2enmod rewrite
#ENV VIRTUAL_HOST 

COPY ./ /var/www/html/

RUN cd /var/www/html && php -r "copy('http://getcomposer.org/installer', 'composer-setup.php');"
RUN cd /var/www/html && php -r "if (hash_file('sha384', 'composer-setup.php') === '756890a4488ce9024fc62c56153228907f1545c228516cbf63f885e036d37e9a59d27d63f46af1d4d07ee0f76181c7d3') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
RUN cd /var/www/html && php composer-setup.php
RUN cd /var/www/html && php -r "unlink('composer-setup.php');"
RUN cd /var/www/html && php composer.phar self-update 2.0.8
RUN cd /var/www/html && php composer.phar install 
#RUN cd /var/www/html && php artisan migrate


RUN  chown -R www-data:www-data /var/www/html/vendor
RUN  chown -R www-data:www-data /var/www/html/storage




CMD ["start-apache"]