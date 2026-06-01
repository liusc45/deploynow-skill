---
name: deploy-now-mezzio
description: Deploy Mezzio (Laminas Mezzio) PSR-15 middleware applications to IONOS Deploy Now. Mezzio is NOT officially detected by Deploy Now (only Laravel and Symfony are per docs.ionos.space), so this skill configures Mezzio as a generic PHP project. Use when the user wants to deploy Mezzio to Deploy Now, needs to set public/ as the publish directory, configure .htaccess for Apache rewrite, write .deploy-now/<project>/config.yaml, run composer clear-config-cache post-deploy, or troubleshoot Mezzio routing on Deploy Now.
metadata:
  author: liusc45
  category: deployment
  workflow_version: v2
---

# Deploy Mezzio to IONOS Deploy Now

This skill helps deploy a **Mezzio** (PSR-15 middleware, part of the Laminas project) application to **IONOS Deploy Now**.

> **Disclaimer.** Mezzio is not officially detected by Deploy Now. Configured as a generic PHP project — **reasonable inference**.

## When to use

- "deploy Mezzio to IONOS"
- "Deploy Now Mezzio setup"
- "Mezzio public/ publish directory Deploy Now"
- "Laminas Mezzio Deploy Now"

## Key facts

1. **Deploy Now generates the workflow.** Customize it, do not replace it.
2. **Document root is `public/`** — set as publish directory.
3. `public/index.php` is the entry point — bootstraps container, loads pipeline and routes.
4. `public/.htaccess` rewrites to `index.php`.
5. **`data/` needs persistence** (cache, config-cache.php).
6. `composer clear-config-cache` must run post-deploy to clear `data/config-cache.php`.
7. v2 placeholders: `$IONOS_DB_HOST`, `$IONOS_APP_URL`.

## Workflow

1. ionos.space → new PHP project → connect repo.
2. Publish directory: **`public`**.
3. Build: `composer install --no-dev --optimize-autoloader`.
4. Provision MariaDB (if using Doctrine).
5. Pull generated workflow → customize.
6. Add `.deploy-now/<project>/config.yaml`, `.env`, `.htaccess.template`.
7. Push.

## Validation checklist

| # | Check |
|---|---|
| 1 | `public/index.php` exists |
| 2 | `composer.json` requires `mezzio/mezzio` |
| 3 | `config/container.php` exists |
| 4 | `config/pipeline.php` exists |
| 5 | `config/routes.php` exists |
| 6 | PHP ≥ 8.1 |
| 7 | `.gitignore` excludes `vendor/`, `data/cache/`, `data/config-cache.php`, `.env` |
| 8 | `.deploy-now/<project>/config.yaml` excludes `data/cache/` |
| 9 | Publish directory = `public` in dashboard |

## Post-deploy commands

```yaml
post-deployment-remote-commands:
  - php8.2-cli composer clear-config-cache
  - php8.2-cli composer development-disable
```

## References

- `templates/deploy-now-config-v2.yaml`
- `templates/env.template`
- `templates/htaccess.template`
- Mezzio docs: https://docs.mezzio.dev/mezzio/
- Mezzio skeleton: https://github.com/mezzio/mezzio-skeleton
