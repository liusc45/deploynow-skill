# Flight PHP on Deploy Now — notes

> **Status: reasonable inference.** Flight is not officially supported by Deploy Now.

## Document root

Flight uses a single `index.php`. Can live at root (`./` as publish dir) or in `public/`.

## Minimal structure

```
├── public/         # ← publish directory (if using public/ structure)
│   ├── .htaccess
│   └── index.php
├── src/
├── vendor/
├── composer.json
└── .env
```

## Flight index.php

```php
<?php
require 'vendor/autoload.php';

Flight::route('/', function () {
    Flight::json(['message' => 'Hello World']);
});

Flight::start();
```

## Source

- https://docs.flightphp.com/
