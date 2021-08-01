FROM alpine:3.12

# Setup document root
RUN mkdir -p /var/www/html
WORKDIR /var/www/html

# Install packages and remove default server definition
RUN apk --no-cache add php7 php7-fpm php7-opcache php7-mysqli php7-json php7-curl \
    php7-zlib php7-xml php7-phar php7-intl php7-dom php7-xmlreader php7-ctype php7-session \
    php7-mbstring php7-gd \
    nginx supervisor curl

# Configure nginx
COPY docker/v2/config/nginx.conf /etc/nginx/nginx.conf
COPY docker/v2/config/app.conf /etc/nginx/conf.d/default.conf

# Configure PHP-FPM
COPY docker/v2/config/fpm-pool.conf /etc/php7/php-fpm.d/www.conf
COPY docker/v2/config/php.ini /etc/php7/conf.d/custom.ini

# Configure supervisord
COPY docker/v2/config/supervisord.conf /etc/supervisor/conf.d/supervisord.conf

# composer install
#RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
#COPY application/composer.json application/composer.lock  /var/www/html/application/
#RUN composer install --working-dir=application --prefer-dist --no-scripts --no-dev --no-autoloader && rm -rf /root/.composer
#RUN composer dump-autoload --working-dir=application --no-scripts --no-dev --optimize

# Add application
COPY . /var/www/html


#user set
RUN adduser -D -g 'www' www
RUN chown -R www:www /var/lib/nginx && \
    chown -R www:www /var/www/html


# Expose the port nginx is reachable on
EXPOSE 80

# Let supervisord start nginx & php-fpm
CMD ["/usr/bin/supervisord", "-c", "/etc/supervisor/conf.d/supervisord.conf"]

# Configure a healthcheck to validate that everything is up & running
HEALTHCHECK --timeout=10s CMD curl --silent --fail http://127.0.0.1:80/fpm-ping
