# Fat-Free Framework on Deploy Now — notes

> **Status: reasonable inference.** F3 is not officially supported by Deploy Now.

## Document root

F3 is flexible. The `index.php` file can live at the repo root or in a subdirectory. If at root, set publish directory to `./`.

## Minimal structure

```
├── app/
│   └── controllers/
├── dict/         # language files
├── lib/          # F3 framework (if not using Composer)
├── tmp/          # cache, logs — MUST persist
├── ui/           # templates
├── index.php     # entry point
├── .htaccess
└── composer.json
```

## F3 .htaccess key rule

```apache
# Block sensitive directories
RewriteRule ^(app|dict|ns|tmp)/|.ini$ - [R=404]
```

## Production config

```php
$f3 = require('lib/base.php');
$f3->set('DEBUG', 0);
$f3->set('CACHE', true);
$f3->set('UI', 'ui/');
```

## Source

- https://fatfreeframework.com/3.9/routing-engine
