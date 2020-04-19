# How to setup WordPress 4.x on Heroku

This will set up a fresh WordPress install on Heroku with the newly released [Heroku Buildpack for PHP](https://github.com/heroku/heroku-buildpack-php).

* `nginx` - Nginx for serving web content.
* `PHP` - PHP-FPM for process management.
* `WordPress` - Downloaded from the Github WordPress Repo.
* `MySQL` - ClearDB for the MySQL backend.
* `Sendgrid` - Sendgrid for the email backend.
* `MemCachier` - MemCachier for the memcached backend.
* `New Relic`- Monitoring

# Getting started

Use the Deploy to Heroku button, or use the old fashioned way described below.

[![Deploy](https://www.herokucdn.com/deploy/button.svg)](https://heroku.com/deploy)

# Clone Project

1) Clone this repository into a new directory.

2) Create your Heroku app.

Note: `application-name`

```bash
heroku apps:create application-name --stack cedar --buildpack https://github.com/heroku/heroku-buildpack-php --region us
```

or on to add this buildpack to an existing app, run

```bash
heroku config:set BUILDPACK_URL=https://github.com/heroku/heroku-buildpack-php
```

3) Before you push to Heroku make sure to add the following add-ons.

```bash
heroku addons:add cleardb
heroku addons:add sendgrid
heroku addons:add memcachier
heroku addons:add papertrail
heroku addons:add newrelic
```

4) Define your AWS keys for the AWS S3 Media Uploader plugin.

```bash
heroku config:set AWS_ACCESS_KEY_ID=123
heroku config:set AWS_SECRET_ACCESS_KEY=123
```
Some default configurations. WP_CACHE=true will enable Batcache with the Memcachier addon.

```bash
heroku config:set WP_ENV=prod
heroku config:set DISABLE_WP_CRON=true
heroku config:set WP_CACHE=true
```

5) Deploy your WordPress site to Heroku.

```bash
git add .
git commit -am "Initial commit"
git push heroku master
```

# Overview

```
└── public                 # Heroku webroot
    ├── content            # The wp-content directory. Renamed to content to avoid confusion with wp-content - and it looks prettier
    │   ├── plugins        # Plugins
    │   ├── mu-plugins     # Required plugins
    │   └── themes         # Your custom themes
    │
    └── wp                 # Where the actual WordPress install will be installed by Composer
```

# Upgrade WordPress

Update the version number for the WordPress package in composer.json, then run `composer update` and commit the changes in composer.json and composer.lock. Do not upgrade WordPress from the admin-interface as it will not survive a restart or dyno change.