## Docker starter kit for Symfony 3.4 tuned up for faster work in Windows Docker.##

Project is ready to work with Symfony 3.4 Includes PHP7.3 + MySQL 5.6 + phpMyadmin + maildev + composer

I didn't tested it on Mac but it should work well.

Project contains 5 containers:
Apache2 on Debian 10
PHP 7.3
MySQL 5.6
phpMyAdmin
maildev

Each container is one linux distribution with one component
Each container have one root access by default
You can install some extras by editing Dockerfile.

## Start docker ##
In host(Windows/Mac) cli
```
docker-compose build
docker-compose up -d
```

Now you can enter in php container as www-data user (default web user in Debian).

```
docker exec -it -u www-data symfony_php_73 bash
```

## Instal new Symfony 3.4 project ##

Now you are in /var/www/symfony which is root folder for aur project.

We can use Composer to install Symfony new project in temp-folder please type:

```
composer create-project symfony/framework-standard-edition temp-folder "3.4.*"
```
 
 Symfony will ask you about parameters to connect database:
 ```
    database_host: mysql
    database_port: 3306
    database_name: symfony
    database_user: symfony
    database_password: symfony
    ...
 ```

When it's done, we will get the project to the root path.

```
cp -Rf /var/www/symfony/temp-folder/. . &&
rm -Rf /var/www/symfony/temp-folder
```

## Use project ##

Now you can open your project:

In browser to http://localhost

and manage database by phpMyAdmin

In browser to http://localhost:8080

## Tuning to speed up project ##
Now you have a working symfony 3.4 project in the docker and everything seems to be ok. Unfortunately, if you use the docker in a different host environment than Linux we will notice a significant slowdown as the application grows.
To prevent this we need to tune the project. For this purpose we copy files from the tune folder to our project.
```
cp -f /var/www/symfony/tune/AppKernel.php /var/www/symfony/app/AppKernel.php &&
cp -f /var/www/symfony/tune/console /var/www/symfony/bin/console &&
cp -f /var/www/symfony/tune/app.php /var/www/symfony/web/app.php &&
cp -f /var/www/symfony/tune/app_dev.php /var/www/symfony/web/app_dev.php &&
cp -f /var/www/symfony/tune/composer.json /var/www/symfony/composer.json
```
at least run ```composer install```

Thats it!
Now we can comfortable work on Symfony project in Windows Docker.

## Tuning explain ##
The bottleneck that slows down the application in Windows Docker is the sharing of the vendor dir, that has a many of files in it. Second reason are cache and log directories.
So i separate the vendor, cache and logs dirs and make them only available in the container and not on the host.
We can find:
vendor dir -> /app-vendor
cache dir -> /app-cache
log dir -> /app-log

Beware! The vendor files are now concealed in your container and won't show anymore on the host. You won't be able to debug the vendors, and autocomplete won't be available in your IDE.
My solution: Install twice all dependencies. Install first them in the standard directory, changing composer.json and then rechange to install dependiencies in container.

You can also add to .gitignore an entry ignoring a folder containing database files ./docker/database .
```
.docker/database/*
```

