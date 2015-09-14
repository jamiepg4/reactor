FROM ubuntu:14.04
MAINTAINER fusioneng <tech@fusion.net>

##
# Operations requiring network connection
##

# Install necessary software
RUN apt-get update \
    && apt-get upgrade -y \
    && apt-get clean \
    && apt-get install -y --no-install-recommends \
      autojump \
      ca-certificates \
      curl \
      git \
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

RUN mkdir -p /srv/www \
  && svn co https://core.svn.wordpress.org/trunk /srv/www/reactor.dev

# Install VIP Go base functionality
WORKDIR /srv/www/reactor.dev
RUN git clone https://github.com/Automattic/vip-mu-plugins-public.git wp-content/mu-plugins

# Install plugins from VIP shared plugins repo
WORKDIR /srv/www/reactor.dev/wp-content/plugins
RUN svn co https://vip-svn.wordpress.com/plugins/bitly/
RUN svn co https://vip-svn.wordpress.com/plugins/cache-nav-menu/
RUN svn co https://vip-svn.wordpress.com/plugins/co-authors-plus/
RUN svn co https://vip-svn.wordpress.com/plugins/fieldmanager/
RUN svn co https://vip-svn.wordpress.com/plugins/msm-sitemap/
RUN svn co https://vip-svn.wordpress.com/plugins/safe-redirect-manager/
RUN svn co https://vip-svn.wordpress.com/plugins/wpcom-thumbnail-editor/
RUN svn co https://vip-svn.wordpress.com/plugins/wpcom-related-posts/
RUN svn co https://vip-svn.wordpress.com/plugins/wp-help/
RUN svn co https://vip-svn.wordpress.com/plugins/zoninator/

# Install testing tools
RUN curl -O  https://phar.phpunit.de/phpunit.phar \
    && chmod +x phpunit.phar \
    && mv phpunit.phar /usr/local/bin/phpunit
RUN git clone --depth 1 git://develop.git.wordpress.org/ /srv/www/wordpress-develop.dev

##
# Operations not requiring network operations (e.g. configuration)
##

# Set up custom config files
ADD files/nginx_default /etc/nginx/sites-available/default
ADD files/nginx.conf /etc/nginx/nginx.conf
ADD files/php.ini /etc/php5/fpm/php.ini
ADD files/php_www.conf /etc/php5/fpm/pool.d/www.conf
ADD files/supervisord.conf /etc/supervisor/conf.d/supervisord.conf
RUN sed -i -e "s/^bind-address.*/bind-address = 0.0.0.0/" /etc/mysql/my.cnf

# Set up WP-CLI
RUN curl -O https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar \
    && chmod +x wp-cli.phar \
    && mv wp-cli.phar /usr/local/bin/wp

# Set up WordPress and WordPress develop
WORKDIR /srv/www/reactor.dev
ADD files/reactor-php-constants reactor-php-constants
RUN service mysql start \
    && mysql -uroot -p -e \
      "CREATE DATABASE reactor DEFAULT CHARACTER SET utf8" \
    && mysql -uroot -p -e \
      "CREATE DATABASE wordpress_tests DEFAULT CHARACTER SET utf8" \
    && wp core config --allow-root \
      --dbname=reactor \
      --dbuser=root \
      --dbhost=localhost \
      --extra-php < reactor-php-constants \
    && rm reactor-php-constants \
    && wp core multisite-install --allow-root \
      --title="Reactor" \
      --url=http://reactor.dev \
      --admin_user=admin \
      --admin_password=admin \
      --admin_email=admin@example.org \
    && wp site create --slug=fusion --title=Fusion --allow-root

# Open up our ports
EXPOSE 80 3306

# Start our services
RUN mkdir -p /var/log/supervisor
CMD /usr/bin/supervisord -n
