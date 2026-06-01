---
name: deploy-now-laminas
description: Deploy Laminas (formerly Zend Framework) and Mezzio applications to IONOS Deploy Now. Laminas/Mezzio is NOT officially detected by Deploy Now (only Laravel and Symfony are per docs.ionos.space), so this skill configures it as a generic PHP project. Use when the user wants to deploy a Laminas MVC or Mezzio middleware app to Deploy Now, needs to set public/ as the publish directory, configure .htaccess for Apache rewrite, write .deploy-now/<project>/config.yaml, or troubleshoot Laminas routing on Deploy Now.
metadata:
  author: liusc45
  category: deployment
  workflow_version: v2
---

# Deploy Laminas / Mezzio to IONOS Deploy Now

This skill helps deploy a **Laminas MVC** or **Mezzio** (PSR-15 middleware) application to **IONOS Deploy Now**.

> **Disclaimer.** Laminas and Mezzio are not officially detected by Deploy Now. Configured as a generic PHP project — **reasonable inference**.

## When to use

- "deploy Laminas to IONOS"
- "deploy Mezzio to IONOS"
- "Deploy Now Laminas setup"
- "Mezzio public/ publish directory Deploy Now"

## Key facts

1. **Deploy Now generates the workflow.** Customize it, do not replace it.
2. **Document root is `public/`** — set as publish directory.
3. `public/.htaccess` rewrites to `index.php`.
4. **`data/` needs persistence** (cache, logs, config-cache.php).
5. v2 placeholders: `$IONOS_DB_HOST`, `$IONOS_APP_URL`.

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
| 2 | `composer.json` requires `laminas/laminas-mvc` or `mezzio/mezzio` |
| 3 | `config/` directory exists |
| 4 | PHP ≥ 8.1 |
| 5 | `.gitignore` excludes `vendor/`, `data/cache/`, `data/log/`, `.env` |
| 6 | `.deploy-now/<project>/config.yaml` excludes `data/cache/`, `data/log/` |
| 7 | Publish directory = `public` in dashboard |

## Post-deploy commands

```yaml
post-deployment-remote-commands:
  - composer clear-config-cache
  # Mezzio only:
  # - composer development-disable
```

## References

- `templates/deploy-now-config-v2.yaml`
- `templates/env.template`
- `templates/htaccess.template`
- Mezzio docs: https://docs.mezzio.dev/mezzio/
- Laminas docs: https://docs.laminas.dev/
