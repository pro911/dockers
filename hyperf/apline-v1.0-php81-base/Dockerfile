# pro911/hyperf:alpine-v1.0-php81-base
#
# @link     https://github.con/pro911
# @document 本文参考 hyperf官网docker 进行制作 用来适配启动hyperf项目的基础镜像 内置了swoole和mongodb，mysql,redis扩展。
# @contact  pro911@qq.com
# @license  https://github.com/pro911/dockers/LICENSE

# 该传参必须参考: https://hub.docker.com/_/alpine/ 最新版本号 build-arg ALPINE_VERSION="3.16"
ARG ALPINE_VERSION

FROM alpine:$ALPINE_VERSION

LABEL maintainer="pro911 Developers <pro911@qq.com>" version="1.0" license="MIT"

##
# ---------- env settings ----------
##
ENV PHP_BUILD_VERSION=${PHP_BUILD_VERSION:-"81"}

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
    # ---------- clear works ----------
    && rm -rf /var/cache/apk/* /tmp/* /usr/share/man /usr/share/php${PHP_BUILD_VERSION} /usr/local/bin/php* \
    && php -v \
    && php -m \
    && echo -e "\033[42;37m Build Completed :).\033[0m\n"