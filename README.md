
# Setting Up Laravel with DDEV : A Step-by-Step Guide

### Prerequisites:
DDEV, Docker and Composer must be installed before proceeding with Laravel installation.

### How to Install Docker & DDEV?
Visit https://www.docker.com/ to download and install the recommended version of Docker for your operating system.

Follow this installation guide https://ddev.readthedocs.io/en/stable/ to install DDEV.

## Laravel installation


Step 1: Creating a New Laravel Project
---
Before we integrate with DDEV, let's set up a new Laravel project. Open your terminal or command line and run:

```
composer create-project --prefer-dist laravel/laravel laravel-ddev
```

----
Step 2: Setting up DDEV for Laravel
---
Navigate to your project directory:

```cd laravel-ddev```

Now, configure a new DDEV project:

```ddev config```

This will prompt you with a series of questions. For most of them, the default values will suffice. When asked for the project type, choose php.

Once that's done, you'll want to adjust the `webserver_type` and `php_version` to values that Laravel supports. Update `.ddev/config.yaml`:
```
webserver_type: nginx-fpm
php_version: "8.0"
```
Note: Always cross-check the PHP version with Laravel's documentation to ensure compatibility.

----
Step 3: Database Configuration
----

Laravel uses `.env` for environment-specific configurations. DDEV provides dynamic database credentials, which we can inject into Laravel's configuration. In your `.env` file, update the database credentials to:

```
DB_CONNECTION=mysql
DB_HOST=db
DB_PORT=3306
DB_DATABASE=db
DB_USERNAME=db
DB_PASSWORD=db
```

Run this command to get phpmyadmin container
```
ddev get ddev/ddev-phpmyadmin
```

----
Step 4: Configuring the Web Server for Laravel
----

Laravel has specific rewrite rules, especially for its public directory. Update the `.ddev/nginx_full/nginx-site.conf` to properly serve Laravel:

Replace the existing server block with:

```
server {
    listen 80; 
    server_name _; 
    root /var/www/html/public;

    index index.php index.html;

    location / {
        try_files $uri $uri/ /index.php?$args;
    }

    location ~ \.php$ {
        try_files $uri /index.php =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass unix:/run/php-fpm.sock;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
}
```

----
Step 5: Starting DDEV
----

Start your DDEV environment:

``` 
ddev start 
```

Once it's up, you can access your Laravel application via the provided URL, usually something like `http://laravel-ddev.ddev.site`.

----
Step 6: Finalizing the Laravel Installation
----

After starting DDEV, run the following commands to complete your Laravel setup:

Migration (Setting up Laravel's default tables):
```
ddev exec php artisan migrate
```

Key Generation (Setting application key):

```
ddev exec php artisan key:generate
```

----
Known Errors
----

Sometime you can faced the port 80 conflict error. At that time you have to run below commands to fix this eroor.

```
ddev stop 
sudo service apache2 stop 
sudo systemctl disable apache2 
sudo systemctl status apache2

ddev start
```
----
Wrapping Up
----

You now have a Laravel environment running smoothly on DDEV. The setup leverages Docker, which ensures that the application will run consistently across different setups, making it easier for team collaborations.

Remember to periodically check both Laravel's and DDEV's official documentation for updates and best practices. This guide should serve as a solid starting point, but the tech landscape is always shifting.

Happy coding :technologist:
