FROM scratch AS project
COPY . /build

FROM jakzal/phpqa:php7.2 AS qa
COPY --from=project /build /build
WORKDIR /build
RUN phplint . && \
    phpa .

FROM composer AS project-vendor
COPY --from=project /build /build
WORKDIR /build
RUN composer install

FROM phpunit/phpunit AS test
COPY --from=project-vendor /build /build
WORKDIR /build
RUN phpunit test/

FROM php:7.2-apache AS release
COPY --from=project-vendor /build /var/www/html/

FROM release AS release-dev
RUN apk add --no-cache --update --virtual buildDeps autoconf g++ make && \
    pecl install xdebug && \
    docker-php-ext-enable xdebug && \
    apk del buildDeps

FROM release
