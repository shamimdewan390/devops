## my prectice for laravel API, nextjs admin and nextjs Shop/store

for this main domain run nextjs project by bellow code
here no need anythink change becouse nextjs default run 3000 port

```
 # For FrontEnd -> Rest
   location /{
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
```

and need to run this project by ```PM2``` libratiy 
```
pm2 start npm --name "molyecom" -- run start
```
this command run your project in 3000 port by nextjs package.json file default config

## for another nextjs project 

for molymart-admin run you need to changae ```package.json``` file for run nextjs prject in 3002 port becouse already 3000 port used

in ```package.json``` file need to update
```
"scripts": {
  "dev": "next dev --turbopack -p 3002",
  "build": "next build",
  "start": "next start -p 3002",
  "lint": "next lint"
}

```

and need to run this project by ```PM2``` libratiy 
```
pm2 start npm --name "molyecom-admin" -- run start
```
this command run your project in 3002 port by nextjs package.json file config

## below full config file

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
