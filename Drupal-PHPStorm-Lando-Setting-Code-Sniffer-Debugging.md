# Drupal + PHPStorm + Lando: Setting Code Sniffer, Debugging


# Prerequisities

Project is based on [Lando](https://docs.devwithlando.io/tutorials/drupal8.html) recipe **drupal8**, webroot is **web** and xdebug is enabled.
**.lando.yml example:**

    recipe: drupal8
    services:
      appserver:
        webroot: web
        xdebug: true


# PHPCS ([Code sniffer](https://github.com/squizlabs/PHP_CodeSniffer))
## Install PHPCS + standards into Drupal project

Add coder to composer.json

    "drupal/coder": "^8.2"

or composer require

    lando composer global require drupal/coder

It will install *squizlabs/php_codesniffer* + Drupal coding standards sniffers.

## Local PHPCS installation

Sometimes could be better to install PHPCS locally rather than install it inside project.
Create some special dir for Coder and install via Composer.

    composer require drupal/coder:8.2.12
## Install PHPCS via Lando

.lando.yml

services:
  appserver:
    build:
      - "composer global require drupal/coder"
      - "/var/www/.composer/vendor/bin/phpcs --config-set installed_paths /var/www/.composer/vendor/drupal/coder/coder_sniffer"
    

If you have PHPCS already installed via Lando, set only path to phpcs (/app/coder/vendor/bin/phpcs) into PHPstorm. 


[ ] TODO: Find out solution: Set path to coding standards to /app/coder/vendor/drupal/coder/coder_sniffer for both Drupal + DrupalPractice standards at once causes error during checking code.


## Optionally (you need it only if you want to check coding standards in CLI)

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

You can use . for current dir.


## Use Lando tooling for checking Drupal coding standard.

Add handy tool for PHPCS.

tooling:
  drupalcs:
    service: appserver
    cmd: "/var/www/.composer/vendor/bin/phpcs --standard=Drupal,DrupalPractice"
    description: Run phpcs Drupal Coding Standards against a given file or directory.
  drupalcbf:
    service: appserver
    cmd: "/var/www/.composer/vendor/bin/phpcs --standard=Drupal"
    description: Automatically fix Drupal coding standards suggestions.

Run new command

    lando phpcs relative_path/to_your_module/folder
    lando phpcs relative_path/to_your_module/file.module


## Drupal Coding standards, Coder, CodeSniffer
1. Set PHP interpreter. 
  1. Go to *File → Settings… → Languages & Frameworks →PHP →CLI Interpreter →Click on the tree dots →Add.* Select *From Docker, Vagrant, VM, Remote…* 
  2. Select *Docker* from radio button options.
  3. Set *Image name* to *devwithlando/php:7.1-fpm* (you can find out the name by executing docker ps command). Uncheck *Visible only for this project*.
![Check Docker container mapping (should be /app, not /opt)](https://d2mxuefqeaa7sj.cloudfront.net/s_CD0F2AB2BC38C7D1DF3A54C2965F4FA0DD0C9C926EDF13F22CB213D0BCD0630C_1519132135927_file.png)

![](https://d2mxuefqeaa7sj.cloudfront.net/s_CD0F2AB2BC38C7D1DF3A54C2965F4FA0DD0C9C926EDF13F22CB213D0BCD0630C_1519132166968_file.png)

1. Go to *File →Settings→Languages & Frameworks→PHP→Code Sniffer*.
2. Add Code Sniffer configuration.
![](https://d2mxuefqeaa7sj.cloudfront.net/s_E3BAFE9CD6847283DD6E2002F30EF0476B89A74B5F299F3A24B29EC244F52065_1523379709805_phpstorm-codesniffer.png)

3. Set PHP Code Sniffer (phpcs) path to */app/vendor/bin/phpcs*.
![](https://d2mxuefqeaa7sj.cloudfront.net/s_E3BAFE9CD6847283DD6E2002F30EF0476B89A74B5F299F3A24B29EC244F52065_1523380034696_phpstorm-codesniffer-path.png)

4. Go to *File →Settings→Editor→Inspection*.
  1. Set Coding standards to *Custom*.
  2. Choose one.
  3. Set Path to ruleset */app/vendor/drupal/coder/coder_sniffer/Drupal/ruleset.xml*.
![](https://d2mxuefqeaa7sj.cloudfront.net/s_E3BAFE9CD6847283DD6E2002F30EF0476B89A74B5F299F3A24B29EC244F52065_1523384196085_phpstorm-inspections.png)



# Debugging
## Debugging from browser
1. **No need to install Xdebug helper extension!**
2. Set breakpoint in PHPstrom.
![](https://d2mxuefqeaa7sj.cloudfront.net/s_E3BAFE9CD6847283DD6E2002F30EF0476B89A74B5F299F3A24B29EC244F52065_1523352437735_phpstorm-breakpoint.png)

3. Start Listening for PHP Debug Connections.
![](https://d2mxuefqeaa7sj.cloudfront.net/s_E3BAFE9CD6847283DD6E2002F30EF0476B89A74B5F299F3A24B29EC244F52065_1523274862834_phpstorm-enable-listening.png)

5. Refresh page in browser. An popup should appear in PHPstrom. Select the appropriate file. After confirming this popup, PHPstorm creates also new server (*File →Settings→Languages & Frameworks→PHP→Servers*) with name appserver.
![](https://d2mxuefqeaa7sj.cloudfront.net/s_E3BAFE9CD6847283DD6E2002F30EF0476B89A74B5F299F3A24B29EC244F52065_1523299805604_phpstorm-debug-popup.png)



## Debugging drush commands from console
1. Set PHP Server. If you already used debugging from browser, this setting should be already present.
![](https://d2mxuefqeaa7sj.cloudfront.net/s_CD0F2AB2BC38C7D1DF3A54C2965F4FA0DD0C9C926EDF13F22CB213D0BCD0630C_1519132807651_file.png)

2. Go to *File →Settings→Language & Frameworks→PHP→Debug*.
![](https://d2mxuefqeaa7sj.cloudfront.net/s_CD0F2AB2BC38C7D1DF3A54C2965F4FA0DD0C9C926EDF13F22CB213D0BCD0630C_1519132623437_file.png)

3. Set path mappings.
  */app* *→ your/local/path/to/project*
**  */app/vendor/drush -> /usr/local/bin/drush*
4. Export environment variable.
    lando ssh appserver
    export PHP_IDE_CONFIG="serverName=appserver"
5. Set breakpoint.
6. Start Listening for PHP Debug Connectios.
![](https://d2mxuefqeaa7sj.cloudfront.net/s_E3BAFE9CD6847283DD6E2002F30EF0476B89A74B5F299F3A24B29EC244F52065_1523274862834_phpstorm-enable-listening.png)

5. Use drush commands from the appserver container.
    lando ssh
    cd web
    drush status

