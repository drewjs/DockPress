FROM ubuntu:16.04

# Install locales
RUN apt update && apt install -y locales
RUN locale-gen en_US.UTF-8

ENV LANG en_US.UTF-8
ENV LANGUAGE en_US:en
ENV LC_ALL en_US.UTF-8

# Get latest version of software-properties-common first
RUN apt update && apt upgrade -y && apt install -y software-properties-common

# Pre-add php7 repo
RUN add-apt-repository -y ppa:ondrej/php \
    && apt update

# Basic Requirements
RUN apt update \
    && apt install -y nginx curl unzip git supervisor wget python-pip sqlite3

# PHP Requirements
RUN apt update \
    && apt install -y php7.1 php7.1-fpm php7.1-cli php7.1-mcrypt php7.1-gd php7.1-mysql \
    php7.1-imap php-memcached php7.1-mbstring php7.1-xml php7.1-curl \
    php7.1-sqlite3

# Wordpress Requirements
RUN apt update \
    && apt install -y libnuma-dev php7.1-intl php-pear php7.1-imagick \
    php7.1-ps php7.1-pspell php7.1-recode php7.1-sqlite php7.1-tidy php7.1-xmlrpc php7.1-xsl

# Install Composer
RUN php -r "readfile('https://getcomposer.org/installer');" | php -- --install-dir=/usr/bin/ --filename=composer

# Install WP-CLI
RUN curl -O https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar \
    && chmod +x wp-cli.phar \
    && mv wp-cli.phar /usr/local/bin/wp

# Install Extra
RUN apt update && apt install -y vim net-tools

# Misc. Cleanup
RUN mkdir -p /run/php \
    && apt remove -y --purge software-properties-common \
    && apt -y autoremove \
    && apt clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* \
    && echo "daemon off;" >> /etc/nginx/nginx.conf \
    && mkdir -p /var/log/nginx \
    && ln -sf /dev/stdout /var/log/nginx/access.log \
    && ln -sf /dev/stderr /var/log/nginx/error.log

# Configuration
COPY nginx.conf /etc/nginx/nginx.conf
COPY default /etc/nginx/sites-available/default
COPY php-fpm.conf /etc/php/7.1/fpm/php-fpm.conf
COPY php.ini /etc/php/7.1/fpm/php.ini
COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf

WORKDIR /var/www/html

# Wordpress permissions
RUN mkdir -p web/app/uploads/cache
RUN chown -R www-data:www-data .
RUN find . -type d -exec chmod 755 {} \;
RUN find . -type f -exec chmod 644 {} \;

EXPOSE 80

CMD ["/usr/bin/supervisord"]
