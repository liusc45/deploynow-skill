# Mezzio on Deploy Now — notes

> **Status: reasonable inference.** Mezzio is not officially supported by Deploy Now.

## Document root

Mezzio serves from `public/`. Entry point is `public/index.php`.

## Production commands

```bash
composer development-disable    # sets APP_ENV=prod, clears config cache
composer clear-config-cache     # removes data/config-cache.php
```

## Directory structure

```
├── config/
│   ├── container.php
│   ├── pipeline.php
│   ├── routes.php
│   └── autoload/
├── data/
│   ├── cache/
│   └── config-cache.php
├── public/
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
