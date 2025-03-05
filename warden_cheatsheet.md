# Warden Cheatsheet
This is a basic outline walkthrough for the setup and management of Docker-based Magento 2 local environments using the Warden CLI utility. For more detailed information, please see the [documentation](https://docs.warden.dev) or the [Github repository](https://github.com/davidalger/warden).

## Warden Setup
0. Before everything, make sure you have [Brew](https://brew.sh/) installed
1. Install [Orbstack (Preferred)](https://orbstack.dev/) or [Docker](https://www.docker.com/get-started/)
2. Install the Warden Homebrew package:
```
brew install wardenenv/warden/warden
```
3. Make sure Orbstack (Docker) Desktop is running
4. Create Warden container:
```
warden svc up
```
Congrats, Warden should now be up and running!

## Project Setup
1. Navigate to your Magento project root directory:
```
cd ~/.../magentoRoot/
```
2. Generate .env file for the project:
```
warden env-init siteName magento2 (siteName will be used as a default domain name "https://siteName.test")
```
Now you should have the .env file in your project's root directory
3. Verify the `.env` file has an appropriate URL (TRAEFIK_DOMAIN), PHP version, etc. Update configurations based on your needs (ex. PHP 8.2, enable opensearch, disable elastic etc.).
4. Ensure you have `app/etc/env.php` file. 
5. Update db connection configurations within `app/etc/env.php` using `magento` as a `dbname`, `username` and `password`. Update the `host` to match the Docker container name for the project database, for example: `siteName_db_1` or it can just be `db`.
6. Update the `app/etc/env.php`  with any other necessary changes (ex. Redis, Elastic). Use this as an example [Warden env.php.init](https://github.com/davidalger/warden-env-magento2/blob/develop/webroot/app/etc/env.php.init.php).
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
or if you have pv installed
```
pv ~/path_to_database_dump.sql.gz | gunzip -c | warden db import
```

Congrats, your project is up and running! Now you can skip to option 4 of Project Startup section (warden shell)

## Project Startup
I you have already build your env previously and you just want to start it just do this:
1. Navigate to Magento root directory:
```
cd ~/.../magentoRoot/
```
2. Ensure the Warden Docker container is running (should be autostarted when docker starting):
```
warden svc start
```
3. Start the project's containers:
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
