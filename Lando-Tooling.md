# Lando Tooling

    tooling:
      cr:
        service: appserver
        description: "Rebuild drupal caches"
        cmd: drush cr -y
        stdout:
          description: Rebuilding drupal caches
      cim:
        service: appserver
        description: "Import drupal configuration"
        cmd: drush cim --source=../src/config -y
        stdout:
          description: Importing drupal configuration
      cex:
        service: appserver
        description: "Export drupal configuration"
        cmd: drush cex --destination=../src/config -y
        stdout:
          description: Exporting drupal configuration
      entup:
        service: appserver
        description: "Drupal entities update"
        cmd: drush entup -y
        stdout:
          description: Updating drupal entities
      updb:
          service: appserver
          description: "Run drupal database updates"
          cmd: drush updb -y
          stdout:
            description: Running drupal database updates



    tooling:
      build-sass:
        service: node
        cmd:
          - cd /app/theme
          - npm run build


      phpcs:
        service: appserver
        cmd: "/app/coder/vendor/bin/phpcs --standard=Drupal,DrupalPractice"
        options:
        description: Run phpcs for given folder or file.

