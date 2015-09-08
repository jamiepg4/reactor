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
      vim \
    && rm -rf /var/lib/apt/lists/*

# Set up custom config files
ADD files/php.ini /etc/php5/fpm/php.ini

# Open up our ports
EXPOSE 80 3306
