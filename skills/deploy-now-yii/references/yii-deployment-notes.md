# Yii2 on Deploy Now — notes

> **Status: reasonable inference.** Yii2 is not officially supported by Deploy Now.

## Document root

Yii2 serves from `web/`. Set this as the publish directory.

## Directory structure (basic template)

```
├── assets/
├── commands/
├── config/
│   ├── console.php
│   └── web.php       # main app config
├── controllers/
├── mail/
├── models/
├── runtime/          # cache, logs — MUST persist
├── views/
├── vendor/
├── web/              # ← publish directory
│   ├── .htaccess
│   ├── index.php
│   ├── css/
│   ├── js/
│   └── assets/       # Yii published assets (auto-generated)
├── composer.json
└── .env
```

## Production config

In `config/web.php`:
- Set `'aliases' => ['@base' => '']`
- Database: use `getenv('DB_HOST')` or `Yii::$app->params['db']`
- Set `YII_DEBUG` to `0` in `.env`

## Migrations

```yaml
post-deployment-remote-commands:
  - php8.2-cli yii migrate --interactive=0
```

## Cache flush

```yaml
post-deployment-remote-commands:
  - php8.2-cli yii cache/flush-all
```

## Source

- https://www.yiiframework.com/doc/guide/2.0/en/start-installation
