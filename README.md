
# Docker Commannds -- All

A docker commands file.

### Docker File (Dockerfile) - 01
```bash
FROM php:8.0.30-apache
WORKDIR /var/www/html

RUN a2enmod rewrite

RUN apt-get update -y && apt-get install -y \
    libicu-dev \
    libmariadb-dev \
    unzip zip \
    zlib1g-dev \
    libpng-dev \
    libjpeg-dev \
    libfreetype6-dev \
    libjpeg62-turbo-dev \
    libpng-dev \
    zlib1g-dev \
    libzip-dev \
    && docker-php-ext-install zip

COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

RUN docker-php-ext-install gettext intl pdo_mysql gd

RUN docker-php-ext-configure gd --enable-gd --with-freetype --with-jpeg \
    && docker-php-ext-install -j$(nproc) gd
```

### Docker Compose (docker-compose.yml) - 02
```bash
services:

  laravel-docker:
    container_name: laravel-docker
    build: .
    volumes:
      - ./laravel-app:/var/www/html
    ports:
      - 9000:80
  
  mysql_db:
    image: mysql:latest
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: taravel_docker
    ports:
    - 3306:3306
  
  phpmyadmin:
    image: phpmyadmin:latest
    ports:
      - 9001:80
    environment:
      - PMA_ARBITRARY=1
```
### MakeFile (docker-compose.yml) - 03
```bash
setup:
	@make build
	@make up 
	@make composer-update
build:
	docker-compose build --no-cache --force-rm
stop:
	docker-compose stop
up:
	docker-compose up -d
composer-update:
	docker exec laravel-docker bash -c "composer update"
data:
	docker exec laravel-docker bash -c "php artisan migrate"
	docker exec laravel-docker bash -c "php artisan db:seed"


# docker exec -it laravel-docker bash
# mysql -h localhost --protocol=TCP -u root -p
# mysql -h localhost --protocol=TCP -u root -p taravel_docker < local_dbname
```
