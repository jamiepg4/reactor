FROM ubuntu:14.04
MAINTAINER fusioneng <tech@fusion.net>

# Install necessary software
RUN apt-get update \
    && apt-get upgrade -y \
    && apt-get clean \
    && apt-get install -y --no-install-recommends \
      autojump \
      ca-certificates \
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
      subversion \
      supervisor \
      vim \
    && rm -rf /var/lib/apt/lists/*

# Set up custom config files
ADD files/nginx_default /etc/nginx/sites-available/default
ADD files/nginx.conf /etc/nginx/nginx.conf
ADD files/php.ini /etc/php5/fpm/php.ini
ADD files/php_www.conf /etc/php5/fpm/pool.d/www.conf
ADD files/supervisord.conf /etc/supervisor/conf.d/supervisord.conf

# Set up WP-CLI
RUN curl -O https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar \
    && chmod +x wp-cli.phar \
    && mv wp-cli.phar /usr/local/bin/wp

# Set up WordPress
RUN mkdir -p /srv/www \
	&& svn co https://core.svn.wordpress.org/trunk /srv/www/reactor.dev
WORKDIR /srv/www/reactor.dev
RUN sed -i -e "s/^bind-address.*/bind-address = 0.0.0.0/" /etc/mysql/my.cnf \
    && service mysql start \
    && mysql -uroot -p -e \
      "CREATE DATABASE reactor DEFAULT CHARACTER SET utf8" \
    && wp core config --allow-root \
      --dbname=reactor \
      --dbuser=root \
      --dbhost=localhost \
    && wp core install --allow-root \
      --title="Reactor" \
      --url=http://reactor.dev \
      --admin_user=admin \
      --admin_password=admin \
      --admin_email=admin@example.org

# Open up our ports
EXPOSE 80 3306

# Start our services
RUN mkdir -p /var/log/supervisor
CMD /usr/bin/supervisord -n
