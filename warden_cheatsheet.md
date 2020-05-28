# Warden Cheatsheet
This is a basic outline walkthrough for the setup and management of Docker-based Magento 2 local environments using the Warden CLI utility. For more detailed information, please see the [documentation](https://docs.warden.dev) or the [Github repository](https://github.com/davidalger/warden).

## Warden Setup
1. Install [Docker Desktop for Mac](https://hub.docker.com/editions/community/docker-ce-desktop-mac)
2. Install the Warden Homebrew package:
```
brew install davidalger/warden/warden
```
3. Make sure Docker Desktop is running
4. Create the Warden Docker container:
```
warden up
```

## Project Setup
1. Navigate to Magento root directory:
```
cd ~/.../magentoRoot/
```
2. Generate .env file for the project:
```
warden env-init siteName magento2 (siteName will be used to generate domain "https://siteName.test")
```
3. Verify the `.env` file has an appropriate URL (TRAEFIK_DOMAIN), PHP version, etc. Make updates to the default configurations as needed.
4. Ensure the project's `app/etc/env.php` file is not a symlink from outside of the `magentoRoot` directory. 
5. Update the db connection configurations within `app/etc/env.php` using `magento` as the value for `dbname`, `username` and `password`. Update the `host` to match the Docker container name for the project database, for example: `siteName_db_1`.
6. Update the env.php with any other necessary changes. (ex. Redis, Elastic) Please see the [Warden env.php.init](https://github.com/davidalger/warden-env-magento2/blob/develop/webroot/app/etc/env.php.init.php) sample for recommendations and requirements of certain configurations.
7. Create the project's Docker container (or rebuild after updating .env configuration):
```
warden env up -d
```
8. Generate an SSL certificate for the project URL:
```
warden sign-certificate siteName.test
```
9. Import a database file:
```
warden db import < ~/path_to_database_dump.sql
```
or
```
pv ~/path_to_database_dump.sql.gz | gunzip -c | warden db import
```

## Project Startup
1. Navigate to Magento root directory:
```
cd ~/.../magentoRoot/
```
2. Esnure the Warden Docker container is running:
```
warden start
```
3. Start the project's Docker container:
```
warden env start
```
4. SSH into the project's container:
```
warden shell
```
5. Run Magento setup commands as needed from within SSH connection:
```
composer install
bin/magento setup:upgrade
bin/magento cache:flush
... any other
```

## Database Management
* Connect to the database from CLI:
```
warden shell
mysql -h siteName_db_1 -umagento -pmagento magento
```
* [Connect to the database from GUI](https://docs.warden.dev/configuration/database.html)

## Stopping a Project Container
```
cd ~/.../magentoRoot/
warden env stop
```

## Stopping the Warden Container
```
warden stop
```
If you are done using Docker Desktop, manually Quit via the GUI program.

## Removing a Project Container
```
cd ~/.../magentoRoot/
warden env down
```

## Removing the Warden Container
```
warden down
```
