
# Virtual Private Server


With this tutorial, you can install your project to any type of blank or empty ubuntu server. For example, `Digital Ocean Droplets, Amazon Lightsail, AWS, Google Cloud Virtual Private Server, Azure Ubuntu Virtual Private Server`, etc.

> If you want to use all the scripts (`shop`, `admin`, `api`) on the same server as this tutorial, then we recommend creating a blank ubuntu-based (`v20.0.4 lts `) or upper server with at least 2+ CPU cores and 2GB+ memory.

> Please connect your `domain` with `server`. We don't recommend/support deployment the project via `IP`.


## Access Server

At first login your server using `SSH` and `Terminal`

## Install NodeJS & Required Application

### Install NodeJS

At first, we've to install NodeJS and npm to run your project if need. To install NodeJS and npm, run this command on your terminal,

```bash
sudo apt-get update
```

```bash
curl -sL https://deb.nodesource.com/setup_16.x | sudo -E bash -
```

```bash
sudo apt-get install -y nodejs
```


```bash
sudo apt update
```

### Install Yarn

To install yarn, use this command,

```bash
sudo npm i -g yarn
```

If you face any permission issue, then please check this official doc to resolve that,

[Npm Permission Issue](https://docs.npmjs.com/resolving-eacces-permissions-errors-when-installing-packages-globally)

### Install Zip & Unzip

```bash
sudo apt install zip unzip vim git
```

### Install PM2

Now we will install `PM2`, which is a process manager for Node.js applications. `PM2` provides an easy way to manage and daemonize applications (run them in the background as a service). To install `PM2` use this command,

```bash
sudo npm install -g pm2
```

After restarting the server or if the server crash, then pm2 will halt the process. To prevent that, we'll add pm2 as a `startup` process to run automatically after restart the server.

```bash
pm2 startup systemd
```

For list
```bash
pm2 list
```

For logs
```bash
pm2 logs
```

## Setup Server

### Introduction

Nginx is one of the most popular web servers in the world. In this deployment tutorial, we're going to use Nginx to host our website. In this tutorial, we're going to use ubuntu 20.04 to host pickbazar

### Step 1 - Installing Nginx

After creating the server, make sure the apt library is up to date. To update the apt library, use this command,

```bash
sudo apt update
```

Add PPA to get the specific php version

```bash
sudo add-apt-repository ppa:ondrej/php
```

```bash
sudo apt update
```

After the update apt, we're going to install Nginx. To do that, use this command

```bash
sudo apt install nginx
```

### Step 2: Adjusting the Firewall

Before testing Nginx, the firewall software needs to be adjusted to allow access to the service. Nginx registers itself as a service with `ufw` upon installation, making it straightforward to allow Nginx access.

To check the `ufw` list, use this command,

```bash
sudo ufw app list
```

You will get a listing of an application list like this,

```
Available applications:
  CUPS
  Nginx Full
  Nginx HTTP
  Nginx HTTPS
```

At first, add ssh to the firewall,

```bash
sudo ufw allow ssh
sudo ufw allow OpenSSH
```

After that, to enable Nginx on the firewall, use this command,

```bash
sudo ufw allow 'Nginx HTTP'
```

Now enable the firewall,

```bash
sudo ufw enable
```

```bash
sudo ufw default deny
```

You can verify the change by typing:

```bash
sudo ufw status
```

The output will be indicated which HTTP traffic is allowed:

![11_Output2](../images/11_Output2.png)

### Step 3 – Checking your Web Server

Now check the status of the Nginx web server by using this command,

```
systemctl status nginx
```

You'll get an output like this,

```
Status: active

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW       Anywhere                  
Nginx HTTP                 ALLOW       Anywhere                  
22/tcp (v6)                ALLOW       Anywhere (v6)             
Nginx HTTP (v6)            ALLOW       Anywhere (v6)
```

### Step 4 -  Install MySQL

```bash
sudo apt install mysql-server
```

### Step 5 -  Install PHP & Composer

```bash
sudo apt install php8.1-fpm php8.1-mysql
```

```bash
sudo apt install php8.1-mbstring php8.1-xml php8.1-bcmath php8.1-simplexml php8.1-intl php8.1-gd php8.1-curl php8.1-zip php8.1-gmp
```

```bash
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php -r "if (hash_file('sha384', 'composer-setup.php') === 'e21205b207c3ff031906575712edab6f13eb0b361f2085f1f1237b7126d785e826a450292b6cfd1d64d92e6563bbde02') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
php composer-setup.php
php -r "unlink('composer-setup.php');"
```

```bash
sudo mv composer.phar /usr/bin/composer
```

### Step 6 -  Create MySQL Database & User

```bash
sudo mysql
```

```bash
CREATE DATABASE pickbazar;

CREATE USER 'pickbazar_user'@'%' IDENTIFIED WITH mysql_native_password BY 'pickbazar1';

GRANT ALL ON pickbazar.* TO 'pickbazar_user'@'%';

FLUSH PRIVILEGES;
```

We use MySQL user name `pickbazar_user` and MYSQL password `pickbazar1`. Make sure you change at least `MySQL` password for security.

### Step 7 -  Change permission for the `www` folder

```bash
sudo chown -R $USER:$USER /var/www/
```

### Step 8 -  Upload API to Server
At first, use this command to create a directory on `/var/www/`

```bash
mkdir /var/www/pickbazar
```

Then, go to your `local computer`

1. Extract the `pickbazar` package that you download from `CodeCanyon.`
2. Rename that folder as `redq-ecommerce`
3. On that folder, you'll get another `folder` called `pickbazar-laravel`.
4. On that folder, you'll get a folder called `api`

Now upload this `api` folder to the server `/var/www/pickbazar/`

### Step 9: Setting Up Server & Project

In this chapter, we'll set up our server and also will set up Reverse Proxy to host all of our sites from the same server.

At first, we'll disable the default configuration.

```bash
sudo rm /etc/nginx/sites-enabled/default
```

### Step 10 -  Create New Nginx for the domain

```bash
sudo touch /etc/nginx/sites-available/pickbazar
```

```bash
sudo nano /etc/nginx/sites-available/pickbazar
```

Add this Nginx config file to that edited file,

> Make Sure You use only one config. If you want to use REST API, then use rest Nginx config or use GraphQL config.

### For REST API

```nginx
server {
    listen 80;

    server_name YOUR_DOMAIN.com;

    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-XSS-Protection "1; mode=block";
    add_header X-Content-Type-Options "nosniff";

    index index.html index.htm index.php;

    charset utf-8;

    # For API
    location /backend {
        alias /var/www/pickbazar/api/public;
        try_files $uri $uri/ @backend;
            location ~ \.php$ {
            include fastcgi_params;
            fastcgi_param SCRIPT_FILENAME $request_filename;
            fastcgi_pass   unix:/run/php/php8.1-fpm.sock;
         }
   }

   location @backend {
      rewrite /backend/(.*)$ /backend/index.php?/$1 last;
   }

   # For FrontEnd -> Rest
   location /{
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }

    location /admin{
        proxy_pass http://localhost:3002/admin;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }

    error_page 404 /index.php;

    location ~ \.php$ {
        fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /\.(?!well-known).* {
        deny all;
    }
}
```

### For GraphQL API

> Skip this step if you want to use REST API

```nginx
server {
    listen 80;

    server_name YOUR_DOMAIN.com;

    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-XSS-Protection "1; mode=block";
    add_header X-Content-Type-Options "nosniff";

    index index.html index.htm index.php;

    charset utf-8;

    # For API
    location /backend {
        alias /var/www/pickbazar/api/public;
        try_files $uri $uri/ @backend;
            location ~ \.php$ {
            include fastcgi_params;
            fastcgi_param SCRIPT_FILENAME $request_filename;
            fastcgi_pass   unix:/run/php/php8.1-fpm.sock;
         }
    }

    location @backend {
      rewrite /backend/(.*)$ /backend/index.php?/$1 last;
    }

    # For FrontEnd -> GraphQL
    location /{
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }

    location /admin{
        proxy_pass http://localhost:3004/admin;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }

    error_page 404 /index.php;

    location ~ \.php$ {
        fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /\.(?!well-known).* {
        deny all;
    }
}
```

> Make sure you change `YOUR_DOMAIN.com` to your specific `domain name`

> You can change `api` path, if you want to change the the domain path for the laravel application


> You can change `admin` path, if you want to change the the domain path for the frontend admin


Save and close the file by typing `CTRL` and `X,` then `Y` and `ENTER` when you are finished.

Then enable the config

```bash
sudo ln -s /etc/nginx/sites-available/pickbazar /etc/nginx/sites-enabled/
```

Make sure you didn’t introduce any syntax errors by typing:

```bash
sudo nginx -t
```

Next, restart Nginx:

```bash
sudo systemctl restart nginx
```

## Secure Server

#### Step 1: Secure Nginx with Let's Encrypt

```bash
sudo apt install certbot python3-certbot-nginx
```

```bash
sudo ufw status

sudo ufw allow 'Nginx Full'
sudo ufw delete allow 'Nginx HTTP'

sudo ufw status
```

```bash
sudo certbot --nginx -d YOUR_DOMAIN
```

> After this command, you'll get several command prompt. Make sure you take the necessary steps and provide information on that command prompt.

## Install API

#### Step 1: Build and Run `api`

At first, go to the `api` folder, then copy `.env.example` to `.env,`

```bash
cd /var/www/pickbazar/api
```

```bash
cp .env.example .env
```

Edit `.env`

```bash
nano .env
```

And add `MySQL,` `stripe,` `mail` or others configuration.

Also, add `https://YOUR_DOMAIN.COM/backend` to `APP_URL`. Without this, the `upload` function will be broken.

![APIDomain.png](../images/APIDomain.png)

Then install all the packages and install `api`

```bash
composer install

php artisan key:generate

php artisan marvel:install
```

> You'll get several confirmations for migration, dummy data, and admin account. Make sure you check the confirmation step and take the necessary actions based on your requirement.

Enable `laravel storage,`

```bash
php artisan storage:link
```

Then give proper `permission` for laravel folder,

```bash
sudo chown -R www-data:www-data storage
sudo chown -R www-data:www-data bootstrap/cache
```

Now, when you go to the `YOUR_DOMAIN/backend` you'll get a `welcome` page like this

![22_API](../images/22_API.png)


## FrontEnd Project Build

> It's not possible to run both REST and GraphQL versions at the same time. So make sure to follow only the `For REST` section or `For GraphQL` section.

> Typescript requires a huge chunk of memory to build the project, so if your server has at least 8gb+ of memory, then you can build the project on your server directly. If not, then build the project on your server, then move the folder to the server then serve the project. We'll do the second method in this tutorial.

> We'll suggest you build the frontend part on your computer and then upload the build file to the server.

Go to your `pickbazar-laravel` folder from your `local computer`.

### Step 1 - Config Next Admin App For /admin Sub Directory

Edit `admin/rest/next.config.js`,

add `basePath` for '/admin'

![basePath.png](../images/basePath.png)

Again,

Edit `admin/graphql/next.config.js`,

add `basePath` for '/admin'

![gqlBasePath.png](../images/gqlBasePath.png)

### Step 2 - Install & Build

go to your `pickbazar-laravel` folder again

To install all the npm packages run this command,

```bash
yarn
```

### Step 3 - Build the project

At first, we've to copy the sample `.env.template` to production `.env` for the shop and admin first.

#### For REST

Go to,

```bash
cd shop
```

then use this command to copy,

```bash
cp .env.template .env
```

Now edit .env and add you `API` url to `.env`

and use

```bash
NEXT_PUBLIC_REST_API_ENDPOINT="https://YOUR_DOMAIN/backend"
```

and

```bash
FRAMEWORK_PROVIDER="rest"
```

then copy `tsconfig.rest.json` content to `tsconfig.json`

After that, go to the `admin -> rest` folder,

```bash
cd ../admin/rest
```

then use this command to copy,

```bash
cp .env.template .env
```

and use

```bash
NEXT_PUBLIC_REST_API_ENDPOINT="https://YOUR_DOMAIN/backend"
```

Then open `shop -> next.config.js` and `admin -> rest -> next.config.js`

and add your domain to `images` object

![Image.jpeg](../images/Image.jpeg)

> If your API is hosted on a subdomain, then add that subdomain with root domain on `next.config.js`

#### For GraphQL

> Skip this step if you want to use REST API

go to `shop` folder from `root` folder,

```bash
cd shop
```

then use this command to copy,

```bash
cp .env.template .env
```

Now edit .env and add you `API` url to `.env`

and use

```bash
NEXT_PUBLIC_GRAPHQL_API_ENDPOINT="https://YOUR_DOMAIN/backend/graphql"
```

and

```bash
FRAMEWORK_PROVIDER="graphql"
```

then copy `tsconfig.graphql.json` content to `tsconfig.json`

After that, go to the `admin -> graphql` folder,

```bash
cd ../admin/graphql
```

then use this command to copy,

```bash
cp .env.template .env
```

Again edit `.env`

and use

```bash
NEXT_PUBLIC_GRAPHQL_API_ENDPOINT="https://YOUR_DOMAIN/backend/graphql"
```

Then open `shop -> next.config.js` and `admin -> graphql -> next.config.js`

and add your domain to `images` object

![Image.jpeg](../images/Image.jpeg)

> If your API is hosted on a subdomain, then add that subdomain with root domain on `next.config.js`

#### Build Project

Now go to the `pickbazar-laravel` folder,

and run,

For `REST api`

```bash
yarn build:shop-rest
yarn build:admin-rest
```

And for `GraphQL`,

> Skip this step if you want to use REST API

```bash
yarn build:shop-gql
yarn build:admin-gql
```

Now zip `admin`, `shop`, `package.json`, `babel.config.js` and `yarn.lock` files and upload them to the server `/var/www/pickbazar`

Now go to the server `/var/www/pickbazar` using terminal

Then `unzip` the `frontend` zip file.

## Install FrontEnd And Run

Then install all the node packages,

```bash
yarn
```

### Run frontend app

> Make Sure You use only one run command. If you want to use REST API, then use the rest command or use the GraphQL command.

#### For REST API

For `shop rest` app, use this command,

```bash
pm2 --name shop-rest start yarn -- run start:shop-rest
```

Then to run the `admin rest` app, use this command,

```bash
pm2 --name admin-rest start yarn -- run start:admin-rest
```

#### For GraphQL API

> Skip this step if you want to use REST API

For `shop gql` app, use this command,

```bash
pm2 --name shop-gql start yarn -- run start:shop-gql
```

Then to run the `admin gql` app, use this command,

```bash
pm2 --name admin-gql start yarn -- run start:admin-gql
```

Now go to Now, go to your `YOUR_DOMAIN` to access the shop page and `YOUR_DOMAIN/admin` for the access admin section.



#only nextjs

```
server {
    listen 80;

    server_name molyecom.test;

    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-XSS-Protection "1; mode=block";
    add_header X-Content-Type-Options "nosniff";

    index index.html index.htm index.php;

    charset utf-8;



   # For FrontEnd -> Rest
   location /{
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }

    location /molyecom-admin{
        proxy_pass http://localhost:3002/;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }

    error_page 404 /index.php;


    location ~ /\.(?!well-known).* {
        deny all;
    }
}

```


# only laravel

```
server {
    listen 80;

    server_name molyecom.test;

    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-XSS-Protection "1; mode=block";
    add_header X-Content-Type-Options "nosniff";

    index index.html index.htm index.php;

    charset utf-8;

    # For API
    location / {
        alias /var/www/molyecom-backend/public;
        try_files $uri $uri/ @backend;
            location ~ \.php$ {
            include fastcgi_params;
            fastcgi_param SCRIPT_FILENAME $request_filename;
            fastcgi_pass   unix:/run/php/php8.3-fpm.sock;
         }
   }

   location @backend {
      rewrite /backend/(.*)$ /backend/index.php?/$1 last;
   }

    error_page 404 /index.php;

    location ~ \.php$ {
        fastcgi_pass unix:/var/run/php/php8.3-fpm.sock;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /\.(?!well-known).* {
        deny all;
    }
}

```
# for laravel and 2 nextjs project 

```
server {
    listen 80;

    server_name molyecom.test;

    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-XSS-Protection "1; mode=block";
    add_header X-Content-Type-Options "nosniff";

    index index.html index.htm index.php;

    charset utf-8;

    # For API
    location /molyecom-backend {
        alias /var/www/molyecom-backend/public;
        try_files $uri $uri/ @backend;
            location ~ \.php$ {
            include fastcgi_params;
            fastcgi_param SCRIPT_FILENAME $request_filename;
            fastcgi_pass   unix:/run/php/php8.3-fpm.sock;
         }
   }

   location @backend {
      rewrite /backend/(.*)$ /backend/index.php?/$1 last;
   }

   # For FrontEnd -> Rest
   location /{
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }

    location /molyecom-admin{
        proxy_pass http://localhost:3002/;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }

    error_page 404 /index.php;

    location ~ \.php$ {
        fastcgi_pass unix:/var/run/php/php8.3-fpm.sock;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /\.(?!well-known).* {
        deny all;
    }
}

```
