# Docker Symfony (PHP7-APACHE - MySQL - ELK)

Docker-symfony gives you everything you need for developing Symfony application. 

## Installation

1. Create a `.env` from the `.env.dist` file. Adapt it according to your symfony application

    ```bash
    cp .env.dist .env
    ```

2. Build/run containers with (with and without detached mode)

    ```bash
    $ docker-compose build
    $ docker-compose up -d
    ```

3. Update your system host file (add symfony.dev)
    
    ```bash
    $ sudo echo "127.0.0.1 symfony.dev" >> /etc/hosts
    ```
        
4. Prepare Symfony app

    1. Update app/config/parameters.yml

        ```yml
        # path/to/your/symfony-project/app/config/parameters.yml
        parameters:
            database_host: mysqldb
            ...
        ```

    2. Composer install & create database

        ```bash
        $ docker-compose exec php bash
        $ composer install
        ```
           
        ```bash   
        # Symfony3
        $ sf doctrine:database:create
        $ sf doctrine:schema:update --force
        $ sf doctrine:fixtures:load
        ```

5. Enjoy :-)

## Usage

Just run `docker-compose up -d`

## How it works?

Have a look at the `docker-compose.yml` file, here are the `docker-compose` built images:

* `db`: This is the MySQL database container,
* `php`: This is the PHP-FPM container in which the application volume is mounted,
* `elk`: This is a ELK stack container which uses Logstash to collect logs, send them into Elasticsearch and visualize them with Kibana,

This results in the following running containers:

```bash
$ docker-compose ps
           Name                          Command               State              Ports            
--------------------------------------------------------------------------------------------------
dockersymfony_db_1            /entrypoint.sh mysqld            Up      0.0.0.0:3306->3306/tcp      
dockersymfony_elk_1           /usr/bin/supervisord -n -c ...   Up      0.0.0.0:81->80/tcp          
dockersymfony_php_1           php7-apache                          Up      0.0.0.0:80->80/tcp      
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
$ docker-compose exec db mysql -uroot -p "root"

# F***ing cache/logs folder
$ sudo chmod -R 777 app/cache app/logs # Symfony2
$ sudo chmod -R 777 var/cache var/logs # Symfony3

# Check CPU consumption
$ docker stats $(docker inspect -f "{{ .Name }}" $(docker ps -q))

# Delete all containers
$ docker rm $(docker ps -aq)

# Delete all images
$ docker rmi $(docker images -q)
```

## FAQ

* How I can add PHPMyAdmin?  
Simply add this: (then go to [symfony.dev:8080](http://symfony.dev:8080))

```
phpmyadmin:
    image: phpmyadmin/phpmyadmin
    ports:
        - "8080:80"
    links:
        - db
```

* Got this error: `ERROR: Couldn't connect to Docker daemon at http+docker://localunixsocket - is it running?
If it's at a non-standard location, specify the URL with the DOCKER_HOST environment variable.` ?  
Run `docker-compose up -d` instead.

* How to config Xdebug?
Xdebug is configured out of the box!
Just config your IDE to connect port  `9001` and id key `PHPSTORM`