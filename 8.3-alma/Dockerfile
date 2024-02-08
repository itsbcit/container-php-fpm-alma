FROM bcit.io/almalinux:8-latest

ENV DISABLE_EXTENSIONS="xdebug curl enchant exif ftp gd imap ldap odbc snmp sysvmsg sysvsem sysvshm pdo_odbc amqp ast imagick mailparse ssh2 vips zmq couchbase oci8"
ENV ENABLE_EXTENSIONS=""

LABEL maintainer="jesse@weisner.ca, chriswood.ca@gmail.com"
LABEL build_id="1707420499"
LABEL php_version="8.3.2"
LABEL alma_version="8"

# PHP
RUN dnf -y  install \
    http://rpms.remirepo.net/enterprise/remi-release-8.rpm \
  && rpmkeys --import /etc/pki/rpm-gpg/RPM-GPG-KEY-remi* /etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-8 \
  && 	dnf config-manager -q --enable remi >/dev/null \
  && dnf config-manager --set-enabled powertools \
  && dnf module reset php \
  && dnf module enable -y php:remi-8.3 \
  && dnf -y install \
    bzip2 \
    fcgiwrap \
    unzip \
    vim \
    zip \
    php-bcmath-8.3.2 \
    php-dba-8.3.2 \
    php-enchant-8.3.2 \
    php-fpm-8.3.2 \
    php-gd-8.3.2 \
    php-gmp-8.3.2 \
    php-imap-8.3.2 \
    php-intl-8.3.2 \
    php-ldap-8.3.2 \
    php-mbstring-8.3.2 \
    php-mysqlnd-8.3.2 \
    php-odbc-8.3.2 \
    php-opcache-8.3.2 \
    php-pdo-8.3.2 \
    php-pdo-dblib-8.3.2 \
    php-process-8.3.2 \
    php-pgsql-8.3.2 \
    php-pspell-8.3.2 \
    php-snmp-8.3.2 \
    php-soap-8.3.2 \
    php-sodium-8.3.2 \
    php-tidy-8.3.2 \
    php-xml-8.3.2 \
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
    php-8.3.2 \
    php-pear \
  && rm -rf /var/cache/dnf

# PECL
COPY --from=local/php-fpm-alma:8.3-alma-builder-b1707420499 /etc/php.d/20-timezonedb.ini /etc/php.d/20-timezonedb.ini
COPY --from=local/php-fpm-alma:8.3-alma-builder-b1707420499 /usr/lib64/php/modules/timezonedb.so /usr/lib64/php/modules/timezonedb.so

# OCI / Instant Client
ADD instantclient.repo /etc/yum.repos.d/instantclient.repo
ADD https://yum.oracle.com/RPM-GPG-KEY-oracle-ol8 /etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
RUN rpmkeys --import /etc/pki/rpm-gpg/RPM-GPG-KEY-oracle \
 && dnf -y -d2 install \
    php-oci8 \
    oracle-instantclient-basic \
    oracle-instantclient-devel \
    oracle-instantclient-sqlplus \
#  && rm -rf /var/cache/yum \
 && echo /usr/lib/oracle/21/client64/lib > /etc/ld.so.conf.d/oracle-instantclient.conf \
 && ldconfig

## PHP configs
ADD https://raw.githubusercontent.com/php/php-src/PHP-8.3.2/php.ini-development /etc/php.ini-development
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
