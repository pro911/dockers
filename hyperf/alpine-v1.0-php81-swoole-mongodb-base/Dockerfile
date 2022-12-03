# pro911/hyperf:alpine-v1.0-php81-swoole-mongodb-base
#
# @link     https://github.con/pro911
# @document 本文参考 hyperf官网docker 进行制作 用来适配启动hyperf项目的基础镜像 内置了swoole和mongodb，mysql,redis扩展。
# @contact  pro911@qq.com
# @license  https://github.com/pro911/dockers/LICENSE

# 该传参必须参考: https://hub.docker.com/_/alpine/ 最新版本号 build-arg ALPINE_VERSION="3.16"
ARG ALPINE_VERSION

FROM alpine:$ALPINE_VERSION

LABEL maintainer="pro911 Developers <pro911@qq.com>" version="1.0" license="MIT"

# PHP扩展包的版本 可以点击https://php.hernandev.com 查看最新版本
ARG PHP_HERNANDEV_VERSION
# swoole版本号
ARG SWOOLE_VERSION
# composer 版本号
ARG COMPOSER_VERSION
# mongodb扩展的版本号 https://pecl.php.net/package/mongodb
ARG MONGODB_VERSION

# trust this project public key to trust the packages. 添加一个php秘钥信任用于安装php的扩展
ADD https://php.hernandev.com/key/php-alpine.rsa.pub /etc/apk/keys/php-alpine.rsa.pub

##
# ---------- env settings ----------
##
ENV SWOOLE_VERSION=${SWOOLE_VERSION:-"5.0.1"} \
    PHP_BUILD_VERSION=${PHP_BUILD_VERSION:-"81"} \
    PHP_HERNANDEV_VERSION=${PHP_HERNANDEV_VERSION:-"3.15"} \
    COMPOSER_VERSION=${COMPOSER_VERSION:-"2.4.4"} \
    MONGODB_VERSION=${MONGODB_VERSION:-"1.15.0"} \
    #  install and remove building packages
    PHPIZE_DEPS="autoconf dpkg-dev dpkg file g++ gcc libc-dev make php81-dev php81-pear pkgconf re2c pcre-dev pcre2-dev zlib-dev libtool automake libaio-dev openssl-dev curl-dev"

##
# ---------- building ----------
##
RUN set -ex \
    && apk update \
    && apk add --no-cache \
    # Install base packages ('ca-certificates' will install 'nghttp2-libs')
    ca-certificates \
    curl \
    wget \
    tar \
    xz \
    tzdata \
    pcre \
    php${PHP_BUILD_VERSION} \
    php${PHP_BUILD_VERSION}-bcmath \
    php${PHP_BUILD_VERSION}-curl \
    php${PHP_BUILD_VERSION}-ctype \
    php${PHP_BUILD_VERSION}-dom \
    php${PHP_BUILD_VERSION}-fileinfo \
    php${PHP_BUILD_VERSION}-gd \
    php${PHP_BUILD_VERSION}-iconv \
    php${PHP_BUILD_VERSION}-mbstring \
    php${PHP_BUILD_VERSION}-mysqlnd \
    php${PHP_BUILD_VERSION}-openssl \
    php${PHP_BUILD_VERSION}-pdo \
    php${PHP_BUILD_VERSION}-pdo_mysql \
    php${PHP_BUILD_VERSION}-pdo_sqlite \
    php${PHP_BUILD_VERSION}-phar \
    php${PHP_BUILD_VERSION}-posix \
    php${PHP_BUILD_VERSION}-redis \
    php${PHP_BUILD_VERSION}-sockets \
    php${PHP_BUILD_VERSION}-sodium \
    php${PHP_BUILD_VERSION}-sysvshm \
    php${PHP_BUILD_VERSION}-sysvmsg \
    php${PHP_BUILD_VERSION}-sysvsem \
    php${PHP_BUILD_VERSION}-simplexml \
    php${PHP_BUILD_VERSION}-tokenizer \
    php${PHP_BUILD_VERSION}-zip \
    php${PHP_BUILD_VERSION}-zlib \
    php${PHP_BUILD_VERSION}-xml \
    php${PHP_BUILD_VERSION}-xmlreader \
    php${PHP_BUILD_VERSION}-xmlwriter \
    php${PHP_BUILD_VERSION}-pcntl \
    php${PHP_BUILD_VERSION}-opcache \
    && ln -sf /usr/bin/php${PHP_BUILD_VERSION} /usr/bin/php \
    # 这条删除 会删除动态库 不能放在安装完swoole.so后面那样会使得动态库丢失
    && apk del --purge *-dev \
    # install extension:swoole < 
    && echo "https://php.hernandev.com/v$PHP_HERNANDEV_VERSION/php-8.1" >> /etc/apk/repositories \
    && echo "@php https://php.hernandev.com/v$PHP_HERNANDEV_VERSION/php-8.1" >> /etc/apk/repositories \
    && apk add --no-cache libstdc++ openssl git bash c-ares-dev libpq-dev \
    && apk add --no-cache --virtual .build-deps $PHPIZE_DEPS \
    #install extension:swoole >
    # download
    && cd /tmp \
    && curl -SL "https://github.com/swoole/swoole-src/archive/v${SWOOLE_VERSION}.tar.gz" -o swoole.tar.gz \
    && ls -alh \
    # php extension:swoole <
    && cd /tmp \
    && mkdir -p swoole \
    && tar -xf swoole.tar.gz -C swoole --strip-components=1 \
    && ln -s /usr/bin/phpize${PHP_BUILD_VERSION} /usr/local/bin/phpize \
    && ln -s /usr/bin/php-config${PHP_BUILD_VERSION} /usr/local/bin/php-config \
    && ( \
    cd swoole \
    && phpize \
    && ./configure --enable-openssl --enable-swoole-curl --enable-cares --enable-swoole-pgsql \
    && make -s -j$(nproc) && make install \
    ) \
    && echo "memory_limit=1G" > /etc/php${PHP_BUILD_VERSION}/conf.d/00_default.ini \
    && echo "opcache.enable_cli = 'On'" >> /etc/php${PHP_BUILD_VERSION}/conf.d/00_opcache.ini \
    && echo "extension=swoole.so" > /etc/php${PHP_BUILD_VERSION}/conf.d/50_swoole.ini \
    && echo "swoole.use_shortname = 'Off'" >> /etc/php${PHP_BUILD_VERSION}/conf.d/50_swoole.ini \
    # php extension:swoole >
    # php extension:mongodb <
    && pecl${PHP_BUILD_VERSION} install mongodb \
    && echo "extension=mongodb.so" > /etc/php${PHP_BUILD_VERSION}/conf.d/mongodb.ini \
    # php extension:mongodb >
    # install composer <
    && wget -nv -O /usr/local/bin/composer https://github.com/composer/composer/releases/download/${COMPOSER_VERSION}/composer.phar \
    && chmod u+x /usr/local/bin/composer \
    # install composer >
    # ---------- clear works ----------
    && apk del .build-deps \
    && rm -rf /var/cache/apk/* /tmp/* /usr/share/man /usr/share/php${PHP_BUILD_VERSION} /usr/local/bin/php* \
    && php -v \
    && php -m \
    && php --ri swoole \
    && php --ri mongodb \
    && php --ri Zend\ OPcache \
    #&& composer \
    && echo -e "\033[42;37m Build Completed :).\033[0m\n"