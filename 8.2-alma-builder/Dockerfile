FROM bcit.io/almalinux:8-latest as BUILDER

RUN dnf -y  install \
    http://rpms.remirepo.net/enterprise/remi-release-8.rpm \
 && rpmkeys --import /etc/pki/rpm-gpg/RPM-GPG-KEY-remi* /etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-8 \
#  && dnf config-manager -q --enable remi-php82 >/dev/null \
 && dnf module reset php \
 && dnf module enable -y php:remi-8.2 \
 && dnf -y  install \
    bzip2 \
    fcgiwrap \
    unzip \
    vim \
    zip \
    gcc \
    php-pear \
    php-devel-8.2.15

RUN pecl channel-update pecl.php.net
RUN touch /etc/php.d/20-timezonedb.ini
RUN pear config-set php_ini /etc/php.d/20-timezonedb.ini
RUN pecl install timezonedb

