FROM ubuntu:14.04
MAINTAINER fusioneng <tech@fusion.net>

# Install necessary software
RUN apt-get update \
    && apt-get upgrade -y \
    && apt-get clean \
    && apt-get install -y --no-install-recommends \
      autojump \
      curl \
      imagemagick \
      memcached \
      mysql-client \
      mysql-server-5.5 \
      nano \
      nginx \
      php5-fpm \
      php5-gd \
      php5-mysql \
      php5-json \
      php5-memcache \
      php5-mcrypt \
      php5-curl \
      php5-cli \
      supervisor \
      vim \
    && rm -rf /var/lib/apt/lists/*

# Set up custom config files
ADD files/nginx_default /etc/nginx/sites-available/default
ADD files/nginx.conf /etc/nginx/nginx.conf
ADD files/php.ini /etc/php5/fpm/php.ini
ADD files/php_www.conf /etc/php5/fpm/pool.d/www.conf
ADD files/supervisord.conf /etc/supervisor/conf.d/supervisord.conf

# Open up our ports
EXPOSE 80 3306

# Start our services
RUN mkdir -p /var/log/supervisor
# CMD ["/usr/bin/supervisord"]
