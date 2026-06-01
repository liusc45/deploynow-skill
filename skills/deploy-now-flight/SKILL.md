---
name: deploy-now-flight
description: Deploy Flight PHP micro-framework applications to IONOS Deploy Now. Flight is NOT officially detected by Deploy Now (only Laravel and Symfony are per docs.ionos.space), so this skill configures Flight as a generic PHP project. Use when the user wants to deploy Flight PHP to Deploy Now, needs to configure .htaccess for Apache rewrite to index.php, write .deploy-now/<project>/config.yaml, or troubleshoot Flight routing on Deploy Now.
metadata:
  author: liusc45
  category: deployment
  workflow_version: v2
---

# Deploy Flight PHP to IONOS Deploy Now

This skill helps deploy a Flight PHP application to **IONOS Deploy Now**.

> **Disclaimer.** Flight is not officially detected by Deploy Now. Configured as a generic PHP project — **reasonable inference**.

## When to use

- "deploy Flight PHP to IONOS"
- "Deploy Now Flight micro-framework setup"
- "Flight PHP Deploy Now"

## Key facts

1. **Deploy Now generates the workflow.** Customize it, do not replace it.
2. **Flight uses a single `index.php` entry point** — publish directory is wherever `index.php` lives (typically repo root or `public/`).
3. `public/.htaccess` rewrites to `index.php`.
4. v2 placeholders: `$IONOS_DB_HOST`, `$IONOS_APP_URL`.

## Workflow

1. ionos.space → new PHP project → connect repo.
2. Publish directory: **`public`** (if using public/ structure) or **`./`** (if index.php is at root).
3. Build: `composer install --no-dev --optimize-autoloader`.
4. Provision MariaDB (if using a DB).
5. Pull generated workflow → customize.
6. Add `.deploy-now/<project>/config.yaml`, `.env`, `.htaccess.template`.
7. Push.

## Validation checklist

| # | Check |
|---|---|
| 1 | `public/index.php` or root `index.php` exists |
| 2 | `composer.json` requires `flightphp/core` |
| 3 | PHP ≥ 8.1 |
| 4 | `.gitignore` excludes `vendor/`, `.env` |
| 5 | `.htaccess` rewrites to `index.php` |

## Post-deploy commands

```yaml
post-deployment-remote-commands:
  - echo "Flight PHP deployment complete"
```

## References

- `templates/deploy-now-config-v2.yaml`
- `templates/env.template`
- `templates/htaccess.template`
- Flight docs: https://docs.flightphp.com/
