
FROM php:8.2-fpm as application

LABEL maintainer="serhii butvin"

ARG DEBIAN_FRONTEND=noninteractive

#ENV APP_ENV $APP_ENV

ARG APP_ENV=$APP_ENV

# build-essential includes tools like gcc, the GNU C compiler, and make, a utility to direct the compilation scripts.
# autoconf is used to generate the configure script that is used later in the compilation.
# libtool is a tool that helps to manage and locate shared libraries.
# bison is YACC-compatible parser generator
# re2c is a tool that is used to generate the PHP's lexer.
# libpng-dev needed by "gd" extension
# libzip-dev needed by "zip" extension
# libicu-dev for intl extension
# libpg-dev for connection to postgres database
# autoconf needed by "redis" extension
ENV BUILD_DEPS \
  build-essential \
  libsasl2-dev \
  libssl-dev \
  python-is-python3


RUN set -ex; \
  apt-get update; \
  apt-get install -y --no-install-recommends \
    curl \
    wget \
    unzip \
    libzip-dev \
    openssh-client \
    cron \
    supervisor \
  ;

RUN set -ex; \
  apt-get update; \
  apt-get install -y --no-install-recommends git; \
  git config --global user.email "application@app.com"; \
  git config --global user.name "Application"; \
  git config --global --add safe.directory "/var/www/html"; \
  git config --global --add safe.directory "/var/www/html/*";

#    git config --global core.fileMode false \
#    git config --global core.autocrlf false \
#    git config --global core.eol lf \
#    git config --global color.ui true \
#    git config --global core.autocrlf input \
#    git config --global core.safecrlf true \
#    git config --global core.ignorecase false \
#    git config --global core.whitespace cr-at-eol \
#    git config --global core.pager "less -x1,5" \
#    git config --global core.editor "vim" \
#    git config --global core.excludesfile ~/.gitignore_global \
#    git config --global alias.st status \


# Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer


ENV LIBRDKAFKA_VERSION v0.11.0

RUN apt-get update \
    && apt-get install -y --no-install-recommends ${BUILD_DEPS} \
    && cd /tmp \
    && git clone \
        --branch ${LIBRDKAFKA_VERSION} \
        --depth 1 https://github.com/edenhill/librdkafka.git librdkafka \
    && cd librdkafka \
    && ./configure \
    && make \
    && make install \
    && pecl install rdkafka \
    && docker-php-ext-enable rdkafka \
    && rm -rf /tmp/librdkafka \
    && apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false \
    ${BUILD_DEPS}


#RUN set -ex; \
#    apt-get update && \
#        apt-get install -y \
#            --no-install-recommends \
#                ${BUILD_DEPS} \
#    ; \
#    cd /tmp && \
#        git clone \
#            --branch ${LIBRDKAFKA_VERSION} \
#            --depth 1 \
#                https://github.com/edenhill/librdkafka.git \
#    ; \
#    cd /tmp/librdkafka && \
#        ./configure && \
#            make && \
#                make install \
#    ; \
#    pecl install rdkafka && \
#        docker-php-ext-enable rdkafka; \
#    rm -rf /tmp/librdkafka
#
#RUN set -ex; \
#    apt-get remove -y \
#        --purge \
#        --auto-remove \
#        --option APT::AutoRemove::RecommendsImportant=false \
#            ${BUILD_DEPS}
#
#RUN set -ex; \
#    docker-php-ext-install \
#        mysqli \
#        pdo pdo_mysql \
#    ; \
#    docker-php-ext-enable \
#        pdo_mysql \
#    ;


# Devtools
RUN set -ex; \
    if [ ${APP_ENV} = "dev" ]; then \
      apt-get update && apt-get install -y --fix-broken \
        bash-completion \
        iputils-ping \
        net-tools \
        acl \
        fish \
        htop \
        tree \
        vim \
      ; \
      pecl install xdebug && docker-php-ext-enable xdebug; \
      echo "xdebug.mode=develop,debug\n" >> "$PHP_INI_DIR"/conf.d/docker-php-ext-xdebug.ini; \
      echo "xdebug.client_port=9003\n" >> "$PHP_INI_DIR"/conf.d/docker-php-ext-xdebug.ini; \
      echo "xdebug.discover_client_host=1\n" >> "$PHP_INI_DIR"/conf.d/docker-php-ext-xdebug.ini; \
      echo "xdebug.idekey=PHPSTORM\n" >> "$PHP_INI_DIR"/conf.d/docker-php-ext-xdebug.ini; \
      echo "xdebug.client_host=172.17.0.1\n" >> "$PHP_INI_DIR"/conf.d/docker-php-ext-xdebug.ini; \
      echo "xdebug.client_host=docker.internal.host\n" >> "$PHP_INI_DIR"/conf.d/docker-php-ext-xdebug.ini; \
      echo "xdebug.start_with_request=yes\n" >> "$PHP_INI_DIR"/conf.d/docker-php-ext-xdebug.ini; \
    fi


RUN set -ex; \
    curl -1sLf 'https://dl.cloudsmith.io/public/symfony/stable/setup.deb.sh' | bash; \
    apt-get update && apt-get install -y symfony-cli


RUN set -ex; \
  docker-php-ext-install \
    zip \
    mysqli \
    pdo \
    pdo_mysql \
  && docker-php-ext-enable \
    pdo_mysql \
  ;


# cleanup
RUN set -ex; \
    apt-get clean; \
    rm -rf \
        /var/lib/apt/lists/* \
        /tmp/* \
        /var/tmp/* \
    ;


WORKDIR /var/www/html


CMD ["php-fpm"]