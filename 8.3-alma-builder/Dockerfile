FROM bcit.io/almalinux:9-latest as BUILDER

RUN dnf -y  install \
    http://rpms.remirepo.net/enterprise/remi-release-9.rpm \
 && rpmkeys --import /etc/pki/rpm-gpg/RPM-GPG-KEY-remi* /etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-9 \
#  && dnf config-manager -q --enable remi-php83 >/dev/null \
 && dnf module reset php \
 && dnf module enable -y php:remi-8.3 \
 && dnf -y  install \
    bzip2 \
    fcgiwrap \
    unzip \
    vim \
    zip \
    gcc \
    php-pear \
    php-devel-8.3.2

RUN pecl channel-update pecl.php.net
RUN touch /etc/php.d/20-timezonedb.ini
RUN pear config-set php_ini /etc/php.d/20-timezonedb.ini
RUN pecl install timezonedb

