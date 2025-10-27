# dev and production brach both config
## step 1
create a file in laravel directory (deploy.sh)

```php
#!/bin/bash
set -e

# Ensure script is executable
chmod +x deploy.sh

echo "Deploying start..."

# Determine branch
BRANCH=$(git rev-parse --abbrev-ref HEAD)
echo "Current branch: $BRANCH"

# Set the deployment path based on branch
if [ "$BRANCH" == "dev" ]; then
    DEPLOY_PATH="/var/www/molyecom_dev_api"
elif [ "$BRANCH" == "production" ]; then
    DEPLOY_PATH="/var/www/molyecom_pro_api"
else
    echo "Branch $BRANCH is not configured for deployment."
    exit 1
fi

cd $DEPLOY_PATH

#remove build folder
sudo rm -rf public/build/
# Pull latest code for the branch
git pull origin $BRANCH

echo "Running migrations..."
php artisan migrate --force
echo "Migrations completed."

npm run build

# Clear caches
php artisan optimize:clear

echo "$BRANCH deployed successfully ✅"


```
## create a file in project and past below code.

.github/workflow/pr_check.yml
```phpname: Deploy to OVH VPS Server 16Core 16GB

on:
  push:
    branches:
      - dev
      - production

jobs:
  deploy-dev:
    if: github.ref == 'refs/heads/dev'
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Deploy to Dev Server
        uses: appleboy/ssh-action@master
        with:
          host: ${{ secrets.SSH_HOST }}
          username: ${{ secrets.SSH_USER }}
          password: ${{ secrets.SSH_PASS }}
          port: 22
          script: |
            cd /var/www/molyecom_dev_api/ || exit 1
            chmod +x deploy.sh
            ./deploy.sh

  deploy-production:
    if: github.ref == 'refs/heads/production'
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Deploy to Production Server
        uses: appleboy/ssh-action@master
        with:
          host: ${{ secrets.SSH_HOST }}
          username: ${{ secrets.SSH_USER }}
          password: ${{ secrets.SSH_PASS }}
          port: 22
          script: |
            cd /var/www/molyecom_pro_api/ || exit 1
            chmod +x deploy.sh
            ./deploy.sh


```
