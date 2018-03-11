# PHPStorm with Lando projects
Here is how to set PHPStorm for Drupal 8 projects with Lando.

- Code Sniffer
- Xdebug


# Prerequisities

Project is based on Lando recipe **drupal8**, webroot is **web** and xdebug is enabled.
**.lando.yml example:**

    recipe: drupal8
    services:
      appserver:
        webroot: web
        xdebug: true
## 
# PHPCS (Code sniffer)
## 1) Install PHPCS + standards into Drupal project

Add coder to composer.json or composer require

    "drupal/coder": "^8.2"


    lando composer require drupal/coder

It will install "squizlabs/php_codesniffer" + Drupal coding standards sniffers.


## 2) PHPCS - Local installation

Sometimes could be better to install PHPCS locally rather than intall it inside of project.
Create some special dir for Coder and install via Composer

    composer require drupal/coder:8.2.12


## 3) Install PHPCS via Lando

If you have PHPCS already installed via Lando (e.g. Jobiqo projects, see [Lando Drupal 7 + Solr](/doc/Lando-Drupal-7-Solr)), set only path to phpcs (/app/coder/vendor/bin/phpcs) into PHPstorm.


## Optionally (you need it only if you want to check coding standards in CLI)
[ ] TODO: Do it via Lando build.

To manually register the `Drupal` and `DrupalPractice` Standard with PHPCS, you must set the installed paths. Go to appserver container.

    lando ssh

You should stay at /app dir.

    /app$ vendor/bin/phpcs --config-set installed_paths /app/vendor/drupal/coder/coder_sniffer

It will create file /vendor/squizlabs/php_codesniffer/CodeSniffer.conf and it should contain the valid path to sniffers. If PHPCS throws an errors, check this file.

Check status for Drupal standards.

    app$ vendor/bin/phpcs -i
    The installed coding standards are PSR2,.... PHPCS, Drupal and DrupalPractice



## Manual PHPCS run

Now, you can run PHPCS manually to check your work.

    app$ /path_to_coder/vendor/bin/phpcs --standard=Drupal,DrupalPractice path_to_module
    
    e.g:
    app$ vendor/bin/phpcs --standard=Drupal,DrupalPractice web/modules/contrib/default_content_deploy/

You can use “.” for current dir.


## PHPstorm configuration

Set PHP interpreter. Start Lando and set Docker. Check Xdebug version.

![Check Docker container mapping (you need /app, not /opt)](https://d2mxuefqeaa7sj.cloudfront.net/s_CD0F2AB2BC38C7D1DF3A54C2965F4FA0DD0C9C926EDF13F22CB213D0BCD0630C_1519132135927_file.png)

![](https://d2mxuefqeaa7sj.cloudfront.net/s_CD0F2AB2BC38C7D1DF3A54C2965F4FA0DD0C9C926EDF13F22CB213D0BCD0630C_1519132166968_file.png)



## Drupal Coding standards, Coder, CodeSniffer

Set Code Sniffer.

![](https://d2mxuefqeaa7sj.cloudfront.net/s_CD0F2AB2BC38C7D1DF3A54C2965F4FA0DD0C9C926EDF13F22CB213D0BCD0630C_1519132286195_file.png)


Set inspections.

![](https://d2mxuefqeaa7sj.cloudfront.net/s_CD0F2AB2BC38C7D1DF3A54C2965F4FA0DD0C9C926EDF13F22CB213D0BCD0630C_1514890650128_file.png)


Set Custom path to right path**,** e.g. **/app/vendor/drupal/coder/coder_sniffer directory**
or /app/coder/vendor/drupal/coder/coder_sniffer/Drupal

That is all. Create nice code! 🙂 




# Debug  from browsers and from drush
## Set PHPstorm
![](https://d2mxuefqeaa7sj.cloudfront.net/s_CD0F2AB2BC38C7D1DF3A54C2965F4FA0DD0C9C926EDF13F22CB213D0BCD0630C_1519132623437_file.png)


Use drush commands from the appserver container!

    lando ssh
    cd web
    drush status



## Set mappings

/app → your/local/path/to/project
/app/vendor/drush -> /usr/local/bin/drush


    export PHP_IDE_CONFIG="serverName=appserver"

The name must correspond with name in PHP Servers settings.

![](https://d2mxuefqeaa7sj.cloudfront.net/s_CD0F2AB2BC38C7D1DF3A54C2965F4FA0DD0C9C926EDF13F22CB213D0BCD0630C_1519132807651_file.png)


Set breakpoints, enable “Start listening” and happy debugging!



