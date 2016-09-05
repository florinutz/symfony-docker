# Docker Symfony (PHP7-FPM - NGINX - MySQL)

[![Build Status](https://api.travis-ci.org/florinutz/symfony-docker.svg?branch=master)](https://travis-ci.org/florinutz/symfony-docker)

Basic setup for developing Symfony applications. It runs with docker and [docker-compose](https://docs.docker.com/compose/). It provides nginx, php7-fpm and mysql.

## Installation

1. Retrieve git project

    ```bash
    $ git clone git@github.com:florinutz/symfony-docker.git
    ```

2. In the docker-compose file, add the path to your Symfony project

    ```yml
    services:
        php:
            volumes:
                - path/to/your/symfony-project:/var/www/symfony
    ```

3. Build containers with (with and without detached mode)

    ```bash
    $ docker-compose up -d
    ```

4. Update your host file (add foo.com)

    ```bash
    $ sudo echo "127.0.0.1 symfony.dev" >> /etc/hosts
    ```

## Usage

Just run `docker-compose up -d`, then:

* Symfony app: visit [foo.com](http://foo.com)
* Symfony dev mode: visit [foo.com/app_dev.php](http://foo.com/app_dev.php)
* Logs (files location): **logs/nginx** and **logs/symfony**

## How it works?

Have a look at the `docker-compose.yml` file, here are the `docker-compose` built images:

* `db`: the MySQL database container
* `php`: the PHP7-FPM container in which the application volume is mounted
* `nginx`: the Nginx webserver container in which application volume is mounted

This results in the following running containers:

```bash
$ docker-compose ps
Name               Command               State              Ports
----------------------------------------------------------------------------
mysql   docker-entrypoint.sh mysql ...   Up      0.0.0.0:3306->3306/tcp
nginx   nginx                            Up      443/tcp, 0.0.0.0:80->80/tcp
php     php-fpm                          Up      0.0.0.0:9000->9000/tcp
```

## Useful commands

```bash
# bash commands
$ docker-compose exec php bash

# Composer (e.g. composer update)
$ docker-compose exec php composer update

# SF commands (Tips: there is an alias inside php container)
$ docker-compose exec php php /var/www/symfony/app/console cache:clear # Symfony2
$ docker-compose exec php php /var/www/symfony/bin/console cache:clear # Symfony3
# Same command by using alias
$ docker-compose exec php bash
$ sf cache:clear

# MySQL commands
$ docker-compose exec mysql mysql -udev -pdev

# Check CPU consumption
$ docker stats $(docker inspect -f "{{ .Name }}" $(docker ps -q))

# Delete all containers
$ docker rm $(docker ps -aq)

# Delete all images
$ docker rmi $(docker images -q)
```

## FAQ

* Permission problem? See [this doc (Setting up Permission)](http://symfony.com/doc/current/book/installation.html#checking-symfony-application-configuration-and-setup)

* How I can add PHPMyAdmin?  
Simply add this: (then go to [foo.com:8080](http://foo.com:8080))

    ```
    phpmyadmin:
        image: phpmyadmin/phpmyadmin
        ports:
            - "8080:80"
        links:
            - mysql
    ```
