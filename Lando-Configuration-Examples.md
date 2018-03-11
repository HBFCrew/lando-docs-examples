# Lando Configuration

## List of Lando Events

https://docs.devwithlando.io/config/events.html


    verbose: Emitting event pre-bootstrap
    verbose: Emitting event post-bootstrap
    verbose: Emitting event pre-instantiate-app
    verbose: Emitting event post-instantiate-app
    verbose: Emitting event app-ready
    verbose: Emitting event pre-cli-load
    verbose: Emitting event task-start-answers
    verbose: Emitting event task-start-run
    verbose: Emitting event pre-instantiate-app
    verbose: Emitting event post-instantiate-app
    verbose: Emitting event app-ready
    verbose: Emitting event status
    verbose: Emitting event status
    verbose: Emitting event pre-engine-stop
    verbose: Emitting event post-engine-stop
    verbose: Emitting event pre-engine-destroy
    verbose: Emitting event post-engine-destroy
    START
    verbose: Emitting event pre-start
    verbose: Emitting event pre-engine-start
    verbose: Emitting event post-engine-start
    verbose: Emitting event pre-engine-start
    verbose: Emitting event post-engine-start
    verbose: Emitting event post-start

.lando.yml

    events:
      post-start
        - appserver: cd $LANDO_WEBROOT && drush cr -y
    
    events:
      post-start:
        - echo "App started!"
      pre-destroy:
        - echo "App before destroy!"


# Lando file examples
## Grav (https://getgrav.org)

.lando.yml

    name: grav-test
    recipe: custom
    services:
      appserver:
        type: php:7.0
        # Serve php by either apache or nginx. You can put in any `service:version`
        # string that is supported by Lando's nginx and apache services.
        via: apache
        version: 2.4
        webroot: web
        # Optionally serve php over https
        ssl: false



## Old PHP project with MySQL

.lando.yml

    name: hqsystem-astrolog
    proxy:
      appserver:
        - astrolog.hqsystem.dev
    services:
        type: php:5.6
        via: apache
        version: 2.4
        ssl: false
        webroot: mywebroot
    
        config:
          # Use a custom php.ini
          conf: config/php/short_tags.ini
    
        # Optionally activate xdebug
        xdebug: true
    
        # These environmental variables will set the default 
        # host/user/port/name/password options for db-import
        overrides:
          services:
            environment:
              DB_HOST: database
              DB_USER: astrolog
              DB_PASSWORD: astrolog
              DB_NAME: astrolog
              DB_PORT: 3306
    
      # My database service
      database:
        type: mysql:5.5
        portforward: 3308
        creds:
          user: astrolog
          password: astrolog
          database: astrolog
        config:
          confd: config/mysql
    
      # Add mailhog capturing
      mailhog:
        type: mailhog
        hogfrom:
          - appserver
        portforward: 1026
    
    # Add some nice command routing tooling:
      php:
        service: appserver
        description: Run PHP command
      mysql:
        user: root
        service: database
        description: Drop into a MySQL shell
      db-import:
        service: appserver
        needs:
          - database
        description: Import into database
        cmd: /helpers/mysql-import.sh
        options:
          host:
            description: The database host
            alias:
              - h
          user:
            description: The database user
            default: root
            alias:
              - u
          database:
            description: The database name
            alias:
              - d
          password:
            description: The database password
            alias:
              - p
          port:
            description: The database port
            default: 3306
            alias:
              - P
      db-export:
        service: appserver
        needs:
          - database
        description: Export a database from your lando app
        cmd: /helpers/mysql-export.sh

./config/mysql/disable_strict_mode.cnf

    [mysqld]
    sql_mode=IGNORE_SPACE,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION

./config/php/short_tags.ini 

    short_open_tag = on
    date.timezone = Europe/Prague


## Drupal 8 project with Behat tests

.lando.yml

    name: myproject
    recipe: drupal8
    proxy:
      appserver:
        - myproject.dev
    services:
      appserver:
        type: php:7.0
        webroot: web
        via: apache
        ssl: true
        xdebug: true
    
        overrides:
          services:
            environment:
              BEHAT_PARAMS: '{"extensions" : {"Behat\\MinkExtension" : {"base_url" : "http://my_domain.dev/"}, "Drupal\\DrupalExtension" : {"drush" : { "root": "/app/web" }}}}'
      database:
        type: mysql:5.7
        portforward: 3310
    
    tooling:
      behat:
        service: appserver
        description: Run behat tests locally.
        cmd:
          - /app/vendor/bin/behat


## Setup environment
    services:
      appserver:
        overrides:
          services:
            environment:
              PHP_IDE_CONFIG: "serverName=my_domain.dev"


## Install additional packages
    services:
      appserver:
        extras:
          - apt-get update -y
          - apt-get install vim -y


## Lock port for external access
    services:
      database:
        portforward: 3307


## Speed up composer
    services:
      appserver:
         overrides:
           volumes:
              ~/.composer:/var/www/.composer

Map public and private files (not checked yet)

    services:
      appserver:
        overrides:
           services:
             volumes:
               - public_files:/app/web/sites/default/files
               - public_files:/app/files/public
               - private_files:/app/files/private

Events example + include custom docker-compose.yml

    events:
      # Run some commands after `lando db-import`. We want the configuration and caches to be up to date.
      post-db-import:
        - appserver: cd $LANDO_WEBROOT && drush cr -y && drush cim -y
    
      # Make sure composer is up to date with the latest required packages.
      post-start:
        # This will improve composer install speed, from 4 min to 30 seconds at least with a full composer install
        # on a drupal project.
        - appserver: composer global require hirak/prestissimo
        - appserver: cd $LANDO_MOUNT && composer install
    # A list of docker compose files to load relative to my app root directory. This follows the docker-compose v3 format,
    # and will be merged with the generated docker-compose file from the .lando.yml configuration.
    compose:
    - compose.yml

