# Laminas / Mezzio on Deploy Now — notes

> **Status: reasonable inference.** Laminas/Mezzio is not officially supported by Deploy Now.

## Document root

Both Laminas MVC and Mezzio serve from `public/`.

## Mezzio entry point (public/index.php)

```php
<?php
declare(strict_types=1);
$container = require 'config/container.php';
$app = $container->get(\Mezzio\Application::class);
$factory = $container->get(\Mezzio\MiddlewareFactory::class);
(require 'config/pipeline.php')($app, $factory, $container);
(require 'config/routes.php')($app, $factory, $container);
$app->run();
```

## Production setup

```bash
composer development-disable   # disables dev mode, clears config cache
composer clear-config-cache    # clears data/config-cache.php
```

## Directory structure (Mezzio skeleton)

```
├── config/
│   ├── container.php
│   ├── pipeline.php
│   └── routes.php
├── data/
│   ├── cache/          # MUST persist
│   └── config-cache.php
├── public/             # ← publish directory
│   ├── .htaccess
│   └── index.php
├── src/
│   └── Handler/
├── vendor/
├── composer.json
└── .env
```

## Source

- https://docs.mezzio.dev/mezzio/
- https://github.com/mezzio/mezzio-skeleton
