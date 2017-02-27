Yii 2 Advanced Project Template
===============================

Yii 2 Advanced Project Template is a skeleton [Yii 2](http://www.yiiframework.com/) application best for
developing complex Web applications with multiple tiers.

The template includes three tiers: front end, back end, and console, each of which
is a separate Yii application.

The template is designed to work in a team development environment. It supports
deploying the application in different environments.

Documentation is at [docs/guide/README.md](docs/guide/README.md).

[![Latest Stable Version](https://poser.pugx.org/yiisoft/yii2-app-advanced/v/stable.png)](https://packagist.org/packages/yiisoft/yii2-app-advanced)
[![Total Downloads](https://poser.pugx.org/yiisoft/yii2-app-advanced/downloads.png)](https://packagist.org/packages/yiisoft/yii2-app-advanced)
[![Build Status](https://travis-ci.org/yiisoft/yii2-app-advanced.svg?branch=master)](https://travis-ci.org/yiisoft/yii2-app-advanced)

DIRECTORY STRUCTURE
-------------------

```
common
    config/              contains shared configurations
    mail/                contains view files for e-mails
    models/              contains model classes used in both backend and frontend
console
    config/              contains console configurations
    controllers/         contains console controllers (commands)
    migrations/          contains database migrations
    models/              contains console-specific model classes
    runtime/             contains files generated during runtime
backend
    assets/              contains application assets such as JavaScript and CSS
    config/              contains backend configurations
    controllers/         contains Web controller classes
    models/              contains backend-specific model classes
    runtime/             contains files generated during runtime
    views/               contains view files for the Web application
    web/                 contains the entry script and Web resources
frontend
    assets/              contains application assets such as JavaScript and CSS
    config/              contains frontend configurations
    controllers/         contains Web controller classes
    models/              contains frontend-specific model classes
    runtime/             contains files generated during runtime
    views/               contains view files for the Web application
    web/                 contains the entry script and Web resources
    widgets/             contains frontend widgets
vendor/                  contains dependent 3rd-party packages
environments/            contains environment-based overrides
tests                    contains various tests for the advanced application
    codeception/         contains tests developed with Codeception PHP Testing Framework
```

Getting Start for deployer https://deployer.org/
=====================================================
Step 1 : Install Deployer
-------------------------
Run the following commands in the console:
```
curl -LO https://deployer.org/deployer.phar
mv deployer.phar /usr/local/bin/dep
chmod +x /usr/local/bin/dep
```
Or you can install it with composer:
```
composer require deployer/deployer
```

Step 2 : Initalize Deployer
---------------------------
Now you can use Deployer via the dep command. Open up a terminal in your project directory and run the following command:
```
dep init
```
This command will create deploy.php file in current directory. It is called recipe and contains configuration and tasks for deployment. By default all recipes extends common recipe.

Step 3 : Remotely
-----------------
You need to set the web root of your site to use current/web:
```
/home/yourname/yoursite.com/current/web
```

How Deployer works
------------------
Deployer makes use of three directories on the server:

releases - contains a number of releases (by default 3).
current - symlink to latest release.
shared - this directory contains files/dirs that are shared between releases.
When the deploy script is run, a new release is created on the server. When the deployment has been completed successfully, the symlink current is updated to point to the latest release.

Recipes
---------
With Deployer, we create our deploy script using modular blocks of code called 'recipes'.

Deployer ships with recipes for common frameworks: deployer/tree/master/recipe
In addition, there is a repository for third-party recipes here: https://github.com/deployphp/recipes

for app advance https://github.com/tejrajs/yii2-app-advance-deploy-recipe
for app basic https://github.com/tejrajs/yii2-app-basic-deploy-recipe

Yii2 app advance recipe
-------------------------

#deploy.php

```
<?php
require_once __DIR__ . '/deployer/recipe/yii2-app-advanced.php';
require_once __DIR__ . '/deployer/recipe/yii-configure.php';
//require_once __DIR__ . '/deployer/recipe/in-place.php';

if (!file_exists (__DIR__ . '/deployer/stage/servers.yml')) {
  die('Please create "' . __DIR__ . '/deployer/stage/servers.yml" before continuing.' . "\n");
}
serverList(__DIR__ . '/deployer/stage/servers.yml');
set('repository', '{{repository}}');

set('default_stage', 'production');

set('keep_releases', 2);

set('writable_use_sudo', false); // Using sudo in writable commands?

task('deploy:configure_composer', function () {
  $stage = env('app.stage');
  if($stage == 'dev') {
    env('composer_options', 'install --verbose --no-progress --no-interaction');
  }
})->desc('Configure composer');

// uncomment the next two lines to run migrations
//after('deploy:symlink', 'deploy:run_migrations');
//after('inplace:configure', 'inplace:run_migrations');

before('deploy:vendors', 'deploy:configure_composer');
before('deploy:vendors', 'deploy:configure_composer');
before('deploy:symlink', 'deploy:configure');

```

#Servers.yml

The deploy script loads configuration values from deployer/stage/servers.yml:
```
# list servers
# -------------
prod_1:
    host: hostname
    user: username
    password: password
    stage: production
    repository: https://github.com/user/repository.git
    deploy_path: /var/www
    app:
        stage: 'prod'

        mysql:
            host: db_server
            username: dbuser
            password: dbpassword
            dbname: dbname

dev_1:
    local: true
    host: localhost
    user: username
    password: password
    stage: local
    repository: https://github.com/user/repository.git
    deploy_path: /home/user/www
    app:
        stage: 'dev'

        mysql:
            host: localhost
            username: dbuser
            password: dbpassword
            dbname: dbname

 ```
Deployment
--------------
In order to deploy our project, we need to create servers.yml in deployer/stage.

Copy the contents of stage/servers-sample.yml as a template to get you started.

Once that is done, we can finally deploy our project using the following command:
```
dep deploy production
```
If we want to see more of what the deploy script is doing, we can pass it the v option:
```
dep deploy production -v
```
If you want even more, add -vv - and for everything, add -vvv

If something goes wrong, we can roll back:
```
dep rollback production
```
We can also deploy locally, provided that one of the servers in the server list has the local flag set (see above):

dep deploy local