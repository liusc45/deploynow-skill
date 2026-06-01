# CakePHP on Deploy Now — notes

> **Status: reasonable inference.** CakePHP is not officially supported by Deploy Now.

## Document root

CakePHP serves from `webroot/`. Set this as the publish directory in the Deploy Now wizard.

## Directory structure

```
├── bin/            # CLI tools (bin/cake)
├── config/         # app.php, app_local.php
├── src/            # Controllers, Models, Templates
├── templates/      # View templates
├── tests/
├── tmp/            # Cache, logs, sessions — MUST persist
├── vendor/
├── webroot/        # ← publish directory
│   ├── .htaccess
│   ├── index.php
│   ├── css/
│   ├── js/
│   └── img/
├── composer.json
└── .env
```

## Production config

In `config/app.php`:
- Set `'debug' => false`
- Set `'Security.salt'` via `env('SECURITY_SALT')`
- Configure database via `env('DB_HOST')` etc.

## Caching

Run `bin/cake cache clear_all` as a post-deploy remote command.

## Migrations

If using CakePHP Migrations plugin:
```yaml
post-deployment-remote-commands:
  - php8.2-cli bin/cake migrations migrate
```

## Source

- https://book.cakephp.org/5/en/installation.html
