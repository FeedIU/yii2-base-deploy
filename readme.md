# Yii2 Base Deploy

Deploy Yii2 Base Application to Server via SSH by RSync

## Config example:

```
name: Build and Deploy
on:
    push:
        branches:
            -   master

jobs:
    build:
        name: Build and Deploy
        runs-on: ubuntu-latest
        steps:
            -   name: Checkout Repository
                uses: actions/checkout@master

            -   name: Setup Enviroment
                uses: shivammathur/setup-php@v2
                with:
                    php-version: '7.1'

            -   name: Speed up the packages installation process
                run: composer global require hirak/prestissimo

            -   name: Install Packages
                run: composer install --no-dev

            -   name: Deploy to Server
                uses: yiier/yii2-base-deploy@master
                with:
                    user: user
                    host: host
                    path: path
                    owner: owner
                env:
                    DEPLOY_KEY: ${{ secrets.DEPLOY_KEY }}
```

## Server permissions

The PHP-FPM worker user must be `nginx`. The deployment restores ownership of
`runtime/` to `nginx:nginx`, with directory permissions `750` and file permissions
`640`, after applying the configured project owner. Yii migrations and translation
extraction also run as `nginx` to keep runtime file ownership consistent.

The SSH deploy user must be allowed to run the permission commands and
`sudo -u nginx php` without a password. Ensure that the CLI PHP version matches
the application requirements and that `nginx` can read the deployed code and
write to the translations directory. Any failed deployment command stops the action.
