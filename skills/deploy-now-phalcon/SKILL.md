---
name: deploy-now-phalcon
description: Deploy Phalcon applications to IONOS Deploy Now. Phalcon is NOT officially detected by Deploy Now (only Laravel and Symfony are per docs.ionos.space), so this skill configures Phalcon as a generic PHP project. Use when the user wants to deploy Phalcon 5 to Deploy Now, needs to set public/ as the publish directory, configure .htaccess with the Phalcon rewrite rules (_url parameter), write .deploy-now/<project>/config.yaml, or troubleshoot Phalcon routing on Deploy Now. Warning — Phalcon requires the phalcon PHP extension; verify Deploy Now supports it before proceeding.
metadata:
  author: liusc45
  category: deployment
  workflow_version: v2
---

# Deploy Phalcon to IONOS Deploy Now

This skill helps deploy a Phalcon application to **IONOS Deploy Now**.

> **Disclaimer.** Phalcon is not officially detected by Deploy Now. Configured as a generic PHP project — **reasonable inference**. **Phalcon requires a C extension (`phalcon.so`)** which may not be available on Deploy Now's shared hosting. Verify before proceeding.

## When to use

- "deploy Phalcon to IONOS"
- "Deploy Now Phalcon setup"
- "Phalcon public/ publish directory Deploy Now"

## Key facts

1. **Phalcon requires the `phalcon` PHP extension.** This is a compiled C extension, not a Composer package. Verify Deploy Now supports custom PHP extensions before investing time.
2. **Deploy Now generates the workflow.** Customize it, do not replace it.
3. **Phalcon document root is `public/`** — set as publish directory.
4. `public/.htaccess` uses `?_url=/$1` rewrite rule (Phalcon-specific).
5. Root `.htaccess` rewrites to `public/`.
6. v2 placeholders: `$IONOS_DB_HOST`, `$IONOS_APP_URL`.

## Workflow

1. **First:** Verify Phalcon extension availability on Deploy Now. Contact support if unsure.
2. ionos.space → new PHP project → connect repo.
3. Publish directory: **`public`**.
4. Build: `composer install --no-dev --optimize-autoloader`.
5. Provision MariaDB.
6. Pull generated workflow → customize (add `phalcon` extension in `Setup PHP` step).
7. Add `.deploy-now/<project>/config.yaml`, `.env`, `.htaccess.template`.
8. Push.

## Validation checklist

| # | Check |
|---|---|
| 1 | `public/index.php` exists |
| 2 | Phalcon extension available (CRITICAL — verify first) |
| 3 | `app/config/config.php` exists |
| 4 | PHP ≥ 8.1 |
| 5 | `.gitignore` excludes `vendor/`, `.env` |
| 6 | Publish directory = `public` in dashboard |

## Post-deploy commands

```yaml
post-deployment-remote-commands:
  - echo "Phalcon deployment complete"
```

## References

- `templates/deploy-now-config-v2.yaml`
- `templates/env.template`
- `templates/htaccess.template`
- Phalcon docs: https://docs.phalcon.io/latest/webserver-setup/
