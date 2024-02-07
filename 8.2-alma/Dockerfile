FROM bcit.io/almalinux:9-latest

ENV DISABLE_EXTENSIONS="xdebug curl enchant exif ftp gd imap ldap odbc snmp sysvmsg sysvsem sysvshm pdo_odbc amqp ast imagick mailparse ssh2 vips zmq couchbase"
ENV ENABLE_EXTENSIONS=""

LABEL maintainer="jesse@weisner.ca, chriswood.ca@gmail.com"
LABEL build_id="1707332622"
LABEL php_version="8.2.15"
LABEL alma_version="9"

RUN dnf -y  install \
    http://rpms.remirepo.net/enterprise/remi-release-9.rpm \
  && rpmkeys --import /etc/pki/rpm-gpg/RPM-GPG-KEY-remi* /etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-9 \
  && 	dnf config-manager -q --enable remi remi-php82 >/dev/null \
  && dnf -y install \
    bzip2 \
    fcgiwrap \
    unzip \
    vim \
    zip \
    php-bcmath-8.2.15 \
    php-dba-8.2.15 \
    php-enchant-8.2.15 \
    php-fpm-8.2.15 \
    php-gd-8.2.15 \
    php-gmp-8.2.15 \
    php-imap-8.2.15 \
    php-intl-8.2.15 \
    php-json-8.2.15 \
    php-ldap-8.2.15 \
    php-mbstring-8.2.15 \
    php-mysqlnd-8.2.15 \
    php-odbc-8.2.15 \
    php-opcache-8.2.15 \
    php-pdo-8.2.15 \
    php-pdo-dblib-8.2.15 \
    php-process-8.2.15 \
    php-pgsql-8.2.15 \
    php-pspell-8.2.15 \
    php-snmp-8.2.15 \
    php-soap-8.2.15 \
    php-sodium-8.2.15 \
    php-tidy-8.2.15 \
    php-xml-8.2.15 \
    php-xmlrpc-8.2.15 \
    php-pecl-amqp \
    php-pecl-ast \
    php-pecl-couchbase \
    php-pecl-event \
    php-pecl-igbinary \
    php-pecl-imagick \
    php-pecl-lzf \
    php-pecl-mailparse \
    php-pecl-mcrypt \
    php-pecl-memcached \
    php-pecl-msgpack \
    php-pecl-oauth \
    php-pecl-protobuf \
    php-pecl-redis \
    php-pecl-ssh2 \
    php-pecl-vips \
    php-pecl-xdebug \
    php-pecl-yaml \
    php-pecl-zip \
    php-pecl-zmq \
    php-pecl-wddx \
    php-8.2.15 \
    php-pear \
  && rm -rf /var/cache/dnf

COPY --from=local/php-fpm:8.2-alma-builder-b1707332622 /etc/php.d/20-timezonedb.ini /etc/php.d/20-timezonedb.ini
COPY --from=local/php-fpm:8.2-alma-builder-b1707332622 /usr/lib64/php/modules/timezonedb.so /usr/lib64/php/modules/timezonedb.so

ADD https://raw.githubusercontent.com/php/php-src/PHP-8.2.15/php.ini-development /etc/php.ini-development
ADD https://raw.githubusercontent.com/docker-library/php/master/docker-php-ext-enable /usr/local/bin/docker-php-ext-enable
ADD https://raw.githubusercontent.com/renatomefi/php-fpm-healthcheck/master/php-fpm-healthcheck /usr/local/bin/php-fpm-healthcheck
ADD zz-docker.conf /etc/php-fpm.d/zz-docker.conf
ADD docker.conf /etc/php-fpm.d/docker.conf
ADD www.conf /etc/php-fpm.d/www.conf
ADD 50-copy-php-fpm-config.sh /docker-entrypoint.d/50-copy-php-fpm-config.sh
ADD 60-php_extensions.sh /docker-entrypoint.d/60-php_extensions.sh
ADD php.ini-development /etc/php.ini-development

# Set timezone, install composer, configure php-fpm 
# ENV TZ=America/Vancouver - set in docker-compose.yml
RUN ln -snf /usr/share/zoneinfo/America/Vancouver /etc/localtime \
 && echo America/Vancouver > /etc/timezone \
 && curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer \
 && mkdir -p /run/php-fpm/ \
 && chmod -R 775 \ 
      /run/php-fpm \
      /etc/php-fpm.d \
      /etc/php.d \
      /usr/local/bin/docker-php-ext-enable \
      /usr/local/bin/php-fpm-healthcheck \
 && chmod 664 \
      /etc/php.ini \
      /etc/php.ini-development 

WORKDIR /application
HEALTHCHECK CMD /usr/local/bin/php-fpm-healthcheck

CMD ["/usr/sbin/php-fpm", "-F", "-O", "-y", "/etc/php-fpm.conf"]
