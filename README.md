# Wordpress Docker
A very simple docker stack for Wordpress development

[![FOSSA Status](https://app.fossa.io/api/projects/git%2Bgithub.com%2FDarwinOnLine%2Fwordpress-docker.svg?type=shield)](https://app.fossa.io/projects/git%2Bgithub.com%2FDarwinOnLine%2Fwordpress-docker?ref=badge_shield)

This complete stack run with docker and [docker-compose (1.7 or higher)](https://docs.docker.com/compose/).

## Installation

1. Create a `.env` from the `.env.dist` file. Adapt it according to your needs.

    ```bash
    cp .env.dist .env
    ```
    
    Customisable vars :
      * `COMPOSE_PROJECT_NAME`: Your project name, the containers will be prefixed by this name. Snake case is advised.
      * `COMPOSE_HOSTNAME`: Your local hostname, like `local.project`. You should add it in your `hosts` file :
        ```
        127.0.0.1	    localhost
        127.0.0.1	    local.project
        ```
      * `SGBD_TYPE`: SGBD type, `mysql` or `mariadb`, see the Docker Hub for available versions : [MySQL](https://hub.docker.com/_/mysql), [MariaDB](https://hub.docker.com/_/mariadb).
      * `MYSQL_ROOT_PASSWORD`: Your MySQL root password.
      * `MYSQL_USER`: Your MySQL user (for Wordpress).
      * `MYSQL_PASSWORD`: Your MySQL user password (for Wordpress).
      * `MYSQL_DATABASE`: Your MySQL database (for Wordpress).
      * `WORDPRESS_IMAGE`: Wordpress image, default to the latest apache version: `wordpress` (See Docker Hub for [Wordpress](https://hub.docker.com/_/wordpress)).
      * `WORDPRESS_TABLE_PREFIX`: Your prefix tables for Wordpress
      * `WORDPRESS_DEBUG`: Enable the debug mode if non-empty.
      * ... and some another options (see details in `.env.dist` and in the Docker Hub for [Wordpress](https://hub.docker.com/_/wordpress)) 

1. Build/run containers (with and without detached mode)

    ```bash
    $ docker-compose build
    $ docker-compose up -d
    ```

1. Wait for some seconds (DB will be initialized & Wordpress tree copied in your workspace)

1. Enjoy :-)

## Entry points & usage

Just run `docker-compose up -d`, then:

* Wordpress: [http://local.project](http://local.project) (or another local domain defined by `COMPOSE_HOSTNAME`) 
* PhpMyAdmin: [http://local.project:81](http://local.project:81) (or another local domain defined by `COMPOSE_HOSTNAME`)

## How it works?

Have a look at the `docker-compose.yml` file, here are the `docker-compose` built images:

* `webp`: This is the web container with an apache and your code,
* `db`: This is the databases container,
* `phpmyadmin`: This is the PhpMyAdmin container, to manage yours databases,

This results in the following running containers:

```bash
$ docker-compose ps
         Name                        Command               State              Ports
-----------------------------------------------------------------------------------------------
your_project_db           docker-entrypoint.sh mysqld      Up      3306/tcp, 33060/tcp
your_project_phpmyadmin   /run.sh supervisord -n           Up      0.0.0.0:81->80/tcp, 9000/tcp
your_project_web          docker-entrypoint.sh apach ...   Up      0.0.0.0:80->80/tcp
```

## What happens when containers are built the first time?

* `db`: The database is initialized and your Wordpress database with dedicated user is created.
* `web`: Wordpress is copied to your workspace.

## Useful commands

```bash
# bash commands
$ docker-compose exec web bash
$ docker-compose exec db bash

# Retrieve an IP Addresses
$ docker inspect -f '{{.Name}} => {{range $key, $value := .NetworkSettings.Networks}}{{.IPAddress}} on {{$key}}{{end}}' $(docker ps -q)

# MySQL commands
$ docker-compose exec db mysql -uroot -p"root"

# Check CPU consumption
$ docker stats $(docker inspect -f "{{ .Name }}" $(docker ps -q))

# Delete all containers
$ docker rm $(docker ps -aq)

# Delete all images
$ docker rmi $(docker images -q)
```

## Work on your plugin / theme
Take a look to the `.gitignore` file.

For a plugin, replace this line:
```
!wordpress/wp-content/themes/.gitkeep
```
with this line:
```
!wordpress/wp-content/plugins/your_plugin
```

For a theme, replace this line:
```
!wordpress/wp-content/themes/.gitkeep
```
with this line:
```
!wordpress/wp-content/themes/your_theme
```
