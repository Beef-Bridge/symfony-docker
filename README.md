# Symfony 4.0 + Docker

[![Build Status](https://travis-ci.org/Beef-Bridge/symfony-docker.svg?branch=master)](https://travis-ci.org/Beef-Bridge/symfony-docker)

##  Requirements

- [Docker](https://docs.docker.com/engine/installation/) installed
- [Docker Compose](https://docs.docker.com/compose/install/) installed

## Services

- PHP-FPM 7.2
- Nginx 1.13
- MySQL 5.7

## Installation

1. Clone this repository
    ```bash
    $ git clone https://github.com/guham/symfony-docker.git
    ```
2. Update the Docker `.env` file according to your needs. The `NGINX_HOST` environment variable allows you to use a custom server name

3. Add the server name in your system host file

4. Copy the `symfony/.env.dist` file to `symfony/.env`
    ```bash
    $ cp symfony/.env.dist symfony/.env
    ```
5. Update the database configuration according to your choice of database

    MySQL:
    ```yaml
    # symfony/config/packages/doctrine.yaml
    doctrine:
        dbal:
            driver: 'pdo_mysql'
            server_version: '5.7'
            charset: utf8mb4
            url: '%env(resolve:DATABASE_URL)%'
            # ...
    ```
    ```bash
    # symfony/.env
    DATABASE_URL=mysql://${MYSQL_USER}:${MYSQL_PASSWORD}@mysql:3306/${MYSQL_DATABASE}
    ```

6. Build & run containers with `docker-compose` by specifying a second compose file, e.g., with MySQL 
    ```bash
    $ docker-compose -f docker-compose.yaml -f docker-compose.mysql.yaml build
    ```
    then
    ```bash
    $ docker-compose -f docker-compose.yaml -f docker-compose.mysql.yaml up -d
    ```

7. Composer install

    first, configure permissions on `symfony/var` folder
    ```bash
    $ docker-compose exec app chown -R www-data:1000 var
    ```
    then
    ```bash
    $ docker-compose exec -u www-data app composer install
    ```

## Access the application

You can access the application both in HTTP and HTTPS:

- with `APP_ENV=dev` or `APP_ENV=prod`: [symfony-docker.localhost](http://symfony-docker.localhost)

**Note:** `symfony-docker.localhost` is the default server name. You can customize it in the `.env` file with `NGINX_HOST` variable.

## Databases

- MySQL

The `MYSQL_DATABASE` variable specifies the name of the database to be created on image startup.
User `MYSQL_USER` with password `MYSQL_PASSWORD` will be created and will be granted superuser access to this database.

## Commands

**Note:** `symfony` is the default value for the user, password and database name. You can customize them in the `.env` file.

```bash
# bash
$ docker-compose exec app /bin/bash

# Symfony console
$ docker-compose exec -u www-data app bin/console

# configure permissions, e.g. on `var/log` folder
$ docker-compose exec app chown -R www-data:1000 var/log

# MySQL
# access with application account
$ docker-compose -f docker-stack.yaml exec mysql mysql -usymfony -psymfony
