# Slim 4 on Deploy Now — notes

> **Status: reasonable inference.** Slim is not officially supported by Deploy Now.

## Document root

Slim 4 serves from `public/`. Set this as the publish directory.

## Directory structure

```
├── app/
│   ├── routes.php
│   └── middleware.php
├── public/           # ← publish directory
│   ├── .htaccess
│   └── index.php
├── src/
│   └── ...
├── vendor/
├── composer.json
└── .env
```

## Slim 4 index.php

```php
<?php
require __DIR__ . '/../vendor/autoload.php';

$app = AppFactory::create();

// Register routes
(require __DIR__ . '/../app/routes.php')($app);

$app->run();
```

## Source

- https://www.slimframework.com/docs/v4/start/web-servers.html
