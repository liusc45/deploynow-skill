---
name: deploy-now-slim
description: Deploy Slim Framework applications to IONOS Deploy Now. Slim is NOT officially detected by Deploy Now (only Laravel and Symfony are per docs.ionos.space), so this skill configures Slim as a generic PHP project. Use when the user wants to deploy Slim 4 to Deploy Now, needs to set public/ as the publish directory, configure .htaccess for Apache rewrite to index.php, write .deploy-now/<project>/config.yaml, or troubleshoot Slim routing on Deploy Now.
metadata:
  author: liusc45
  category: deployment
  workflow_version: v2
---

# Deploy Slim 4 to IONOS Deploy Now

This skill helps deploy a Slim 4 application to **IONOS Deploy Now**.

> **Disclaimer.** Slim is not officially detected by Deploy Now. Configured as a generic PHP project — **reasonable inference**.

## When to use

- "deploy Slim to IONOS"
- "Deploy Now Slim 4 setup"
- "Slim public/ publish directory Deploy Now"

## Key facts

1. **Deploy Now generates the workflow.** Customize it, do not replace it.
2. **Slim document root is `public/`** — set as publish directory.
3. `public/.htaccess` rewrites to `index.php`.
4. v2 placeholders: `$IONOS_DB_HOST`, `$IONOS_APP_URL`.

## Workflow

1. ionos.space → new PHP project → connect repo.
2. Publish directory: **`public`**.
3. Build: `composer install --no-dev --optimize-autoloader`.
4. Provision MariaDB (if using a DB).
5. Pull generated workflow → customize.
6. Add `.deploy-now/<project>/config.yaml`, `.env`, `.htaccess.template`.
7. Push.

## Validation checklist

| # | Check |
|---|---|
| 1 | `public/index.php` exists |
| 2 | `composer.json` requires `slim/slim` or `slim/psr7` |
| 3 | PHP ≥ 8.1 |
| 4 | `.gitignore` excludes `vendor/`, `.env` |
| 5 | Publish directory = `public` in dashboard |

## Post-deploy commands

```yaml
post-deployment-remote-commands:
  - echo "Slim deployment complete"
```

## References

- `templates/deploy-now-config-v2.yaml`
- `templates/env.template`
- `templates/htaccess.template`
- Slim docs: https://www.slimframework.com/docs/v4/start/web-servers.html
