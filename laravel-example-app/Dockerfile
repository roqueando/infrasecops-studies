FROM bitnami/laravel:9.5.1-debian-11-r4

WORKDIR /app

COPY . /app

RUN composer install

ENTRYPOINT ["php", "artisan"]

EXPOSE 8000

