---
name: deploy-now-cakephp
description: Deploy CakePHP applications to IONOS Deploy Now. CakePHP is NOT officially detected by Deploy Now (only Laravel and Symfony are per docs.ionos.space), so this skill configures CakePHP as a generic PHP project. Use when the user wants to deploy CakePHP 4 or 5 to Deploy Now, needs to set webroot/ as the publish directory, configure .htaccess for Apache rewrite, write .deploy-now/<project>/config.yaml, or troubleshoot CakePHP routing issues on Deploy Now.
metadata:
  author: liusc45
  category: deployment
  workflow_version: v2
---

# Deploy CakePHP to IONOS Deploy Now

This skill helps deploy a CakePHP 4/5 application to **IONOS Deploy Now**.

> **Disclaimer.** CakePHP is not officially detected by Deploy Now. This skill configures it as a generic PHP project — **reasonable inference** from Deploy Now's generic PHP support.

## When to use

- "deploy CakePHP to IONOS"
- "Deploy Now CakePHP setup"
- "CakePHP webroot publish directory Deploy Now"

## Key facts

1. **Deploy Now generates the workflow.** Customize the generated file, do not write it.
2. **CakePHP document root is `webroot/`** — set as the publish directory in Deploy Now dashboard.
3. Root `.htaccess` rewrites everything to `webroot/`; `webroot/.htaccess` rewrites to `index.php`.
4. **`logs/` and `tmp/` need persistence** — add to `recurring.excludes`.
5. Placeholder syntax (v2): `$IONOS_DB_HOST`, `$IONOS_DB_NAME`, `$IONOS_DB_USERNAME`, `$IONOS_DB_PASSWORD`, `$IONOS_APP_URL`.

## Workflow

1. Sign in at [ionos.space](https://ionos.space).
2. New PHP project → connect repo.
3. Publish directory: **`webroot`**.
4. Build: `composer install --no-dev --optimize-autoloader`.
5. Provision MariaDB.
6. Pull generated workflow → customize PHP version + extensions.
7. Add `.deploy-now/<project>/config.yaml`, `.env`, `.htaccess.template`.
8. Push.

## Validation checklist

| # | Check |
|---|---|
| 1 | `webroot/index.php` exists |
| 2 | `composer.json` requires `cakephp/cakephp` |
| 3 | `config/app.php` exists |
| 4 | PHP ≥ 8.1 |
| 5 | `.gitignore` excludes `vendor/`, `tmp/`, `logs/`, `.env` |
| 6 | `.deploy-now/<project>/config.yaml` excludes `tmp/`, `logs/` |
| 7 | Publish directory = `webroot` in dashboard |

## Post-deploy commands

```yaml
post-deployment-remote-commands:
  - php8.2-cli bin/cake cache clear_all
```

## References

- `templates/deploy-now-config-v2.yaml`
- `templates/env.template`
- `templates/htaccess.template`
- CakePHP docs: https://book.cakephp.org/5/en/installation.html
