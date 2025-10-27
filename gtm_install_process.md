# GTM Documentations
> see this below link for more details
```php
https://medium.com/@arshakilmahmud/how-i-automated-hosting-my-gtm-server-using-a-simple-bash-script-809f9ff9e0eb
```

## Server - Tag ID-> (login google rag manager -> create accout (select server))
aWQ9R1RNLVRHNkpUNTZCJmVudj0xJmF1dGg9WDJXZEw0YmxZbm9IdHBnX3RPTmtIQQ==


# Dcoker engine install

```php
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done
```

```php
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```


## Install Docker -> 
```php
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```


## Stauts & Start 
```php
sudo systemctl start docker
```
```php
sudo systemctl status docker
```

## Check Hello -> 
```php
sudo docker run hello-world
```

##  Permissions Assign: 
```php
sudo usermod -aG docker $USER
```
```php
newgrp docker
```


## GTM Install Docker Compose code. 
```php
docker run -d \
  --restart unless-stopped \
  -p 5201:8080 \
  -e CONTAINER_CONFIG='------YOUR-CONFIG-HERE------' \
  gcr.io/cloud-tagging-10302018/gtm-cloud-image:stable
```
## or with custom name
```php
  docker run -d \
  --name gtm \
  --restart unless-stopped \
  -p 8080:8080 \
  -e CONTAINER_CONFIG='aWQ9R1RNLUtLWEtaWkhIJmVudj0xJmF1dGg9R2tOcUlEeDJTRWxJRXc4U3lraEkzUQ==' \
  gcr.io/cloud-tagging-10302018/gtm-cloud-image:stable
```

## Nginx Install 
```php
sudo apt update
```
```php
sudo apt install nginx -y
```
```php
nginx -v
sudo systemctl status nginx
sudo systemctl start nginx
sudo systemctl enable nginx
```

##  Certbot Install ->
```php
sudo apt update
sudo apt install certbot python3-certbot-nginx -y
```

## For a Domain 
Domain For config.
Then reload NGINX:

```php
sudo vim /etc/nginx/sites-available/kitcheneshop.xyz
```
## server config code
```php
server {
    listen 80;
    server_name kitcheneshop.xyz www.kitcheneshop.xyz;


    location /gtm/ {
        proxy_pass http://localhost:8080;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

## syblink. 
```php
sudo ln -s /etc/nginx/sites-available/kitcheneshop.xyz /etc/nginx/sites-enabled/
```

## Nginx Reload -> & check config ->
```php
sudo nginx -t
sudo systemctl reload nginx
```

## SSL Certificate
```php
sudo certbot --nginx -d kitcheneshop.xyz -d www.kitcheneshop.xyz
```
# now test it's return "ok"
```php
https://kitcheneshop.xyz/gtm/healthy
```
## Note: if server cofing only ```location / {``` then run only

```php
https://kitcheneshop.xyz/healthy
```













