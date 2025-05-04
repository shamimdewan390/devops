server {
    listen 80;
    server_name shop.koba.test;

    # Set some security headers
    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-XSS-Protection "1; mode=block";
    add_header X-Content-Type-Options "nosniff";

    index index.php index.html index.htm index.nginx-debian.html;
    charset utf-8;

    # For Laravel application
    location / {
        root /var/www/laravel-app/public;
        try_files $uri $uri/ /index.php?$query_string;
    }

    # For PHP processing (Laravel)
    location ~ ^/index.php$ {
        fastcgi_pass unix:/run/php/php8.1-fpm.sock;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME /var/www/laravel-app/public$fastcgi_script_name;
        include fastcgi_params;
    }

    # Laravel specific: Redirect to index.php for all requests
    location ~ \.php$ {
        fastcgi_pass unix:/run/php/php8.1-fpm.sock;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME /var/www/laravel-app/public$fastcgi_script_name;
        include fastcgi_params;
    }

    # Prevent access to hidden files (like .env)
    location ~ /\.(?!well-known).* {
        deny all;
    }

    # Error handling (optional)
    error_page 404 /index.php;
    location = /index.php {
        fastcgi_pass unix:/run/php/php8.1-fpm.sock;
        fastcgi_param SCRIPT_FILENAME /var/www/laravel-app/public$fastcgi_script_name;
        include fastcgi_params;
    }
}
