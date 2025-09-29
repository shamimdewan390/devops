## step 1
create a file in laravel directory (deploy.sh)

```
#!/bin/bash
set -euo pipefail

cd /var/www/molyecom_pro_api

echo ">>> Pulling latest code from production branch..."
git fetch origin production
git reset --hard origin/production

echo ">>> Running migrations..."
php artisan migrate --force

echo ">>> Clearing & caching Laravel configs..."
php artisan cache:clear
php artisan config:clear


echo ">>> Deployment finished!"

```
