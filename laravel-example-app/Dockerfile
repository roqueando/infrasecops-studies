FROM bitnami/laravel:9.5.1-debian-11-r4

WORKDIR /app

COPY . /app

RUN composer install

ENTRYPOINT ["php", "artisan"]

CMD ["serve", "--host=0.0.0.0", "--port=8000"]

EXPOSE 8000

