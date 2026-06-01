---
name: deploy-now-yii
description: Deploy Yii2 applications to IONOS Deploy Now. Yii2 is NOT officially detected by Deploy Now (only Laravel and Symfony are per docs.ionos.space), so this skill configures Yii2 as a generic PHP project. Use when the user wants to deploy Yii2 to Deploy Now, needs to set web/ as the publish directory, configure .htaccess for Apache rewrite, write .deploy-now/<project>/config.yaml, or troubleshoot Yii2 routing on Deploy Now.
metadata:
  author: liusc45
  category: deployment
  workflow_version: v2
---

# Deploy Yii2 to IONOS Deploy Now

This skill helps deploy a Yii2 application to **IONOS Deploy Now**.

> **Disclaimer.** Yii2 is not officially detected by Deploy Now. Configured as a generic PHP project — **reasonable inference**.

## When to use

- "deploy Yii2 to IONOS"
- "Deploy Now Yii2 setup"
- "Yii2 web/ publish directory Deploy Now"

## Key facts

1. **Deploy Now generates the workflow.** Customize it, do not replace it.
2. **Yii2 document root is `web/`** — set as publish directory.
3. `web/.htaccess` rewrites to `index.php`.
4. **`runtime/` needs persistence** — cache, logs.
5. v2 placeholders: `$IONOS_DB_HOST`, `$IONOS_DB_NAME`, `$IONOS_DB_USERNAME`, `$IONOS_DB_PASSWORD`.

## Workflow

1. ionos.space → new PHP project → connect repo.
2. Publish directory: **`web`**.
3. Build: `composer install --no-dev --optimize-autoloader`.
4. Provision MariaDB.
5. Pull generated workflow → customize.
6. Add `.deploy-now/<project>/config.yaml`, `.env`, `.htaccess.template`.
7. Push.

## Validation checklist

| # | Check |
|---|---|
| 1 | `web/index.php` exists |
| 2 | `composer.json` requires `yiisoft/yii2` |
| 3 | `config/web.php` exists |
| 4 | PHP ≥ 8.1 |
| 5 | `.gitignore` excludes `vendor/`, `runtime/`, `.env` |
| 6 | `.deploy-now/<project>/config.yaml` excludes `runtime/` |
| 7 | Publish directory = `web` in dashboard |

## Post-deploy commands

```yaml
post-deployment-remote-commands:
  - php8.2-cli yii cache/flush-all
  - php8.2-cli yii migrate --interactive=0
```

## References

- `templates/deploy-now-config-v2.yaml`
- `templates/env.template`
- `templates/htaccess.template`
- Yii2 docs: https://www.yiiframework.com/doc/guide/2.0/en/start-installation
