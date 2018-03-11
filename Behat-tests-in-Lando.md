# Behat tests in Lando

# Installation
## Lando file

Add behat section (bold typed) into a .lando.yml.

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
              BEHAT_PARAMS: '{"extensions" : {"Behat\\MinkExtension" : {"base_url" : "http://project_url.dev/"}, "Drupal\\DrupalExtension" : {"drush" : { "root": "/app/web" }}}}'

Optionally you can add a new tooling section for running the behat tests by command:
**lando behat**

    tooling:
      behat:
        service: appserver
        description: Run behat tests locally.
        cmd:
          - /app/vendor/bin/behat


## Composer.json file

You need to add these libraries and modules into require-dev section of your project’s **composer.json**:

    "require-dev": {
        "behat/behat": "^3.4",
        "drupal/drupal-extension": "^3.4",
        "drush-ops/behat-drush-endpoint": "^0.0.4"
    },

Add the section above manually and run 

    composer install

or

    lando composer install

or add each lib by 

    composer require <name> --dev

You can add your own composer command for running a tests in script section.

    "scripts": {
        "behat": "./vendor/bin/behat --config=tests/behat.yml --suite=global_features"
    },

Then you can run tests by command

    composer behat

in appserver container or

    lando composer behat

from the project directory. It is very important, because in other directory composer doesn't know the custom command above (behat). You can check all available commands via

    lando composer


# More sources

Good reference for behat commands is in +Behat Drupal.

