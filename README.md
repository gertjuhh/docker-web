# docker-web
Local development stack containing PHP, MySQL, NGINX and MailHog

## Running
```
docker-compose up -d
```

## User definable variables
`.env.dist` contains a list of variables which can be adjusted to your specific needs.  
Copy you local changes to `.env` and adjust accordingly.  
Some variable changes require extra steps before they take effect. 

##### PHP_VERSION
In order to update the PHP version, the container should be rebuilt.  
```
docker-compose up -d --build php
``` 

##### MYSQL_*
Some changes, like a version downgrade, require the storage to be reset.  
*!! This will erase the persisted data, handle with care !!*  
```
docker-compose down -v
```

## php.ini changes
Changes to the `php.ini` file require the PHP container to be rebuilt.  
```
docker-compose up -d --build php
```

## Email
MailHog contains an SMTP server which captures all outgoing mail.  
SMTP: `mailhog:1025`  
UI: `localhost:8025`

## Initial setup
```
echo "APP_ENV=dev" > sulu/.env.local
docker-compose exec php composer install
docker-compose exec php bin/adminconsole sulu:build dev
```

## Composer
```
docker-compose exec php composer <command>
```
