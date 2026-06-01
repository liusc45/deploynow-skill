---
name: deploy-now-fatfree
description: Deploy Fat-Free Framework (F3) applications to IONOS Deploy Now. F3 is NOT officially detected by Deploy Now (only Laravel and Symfony are per docs.ionos.space), so this skill configures F3 as a generic PHP project. Use when the user wants to deploy Fat-Free Framework to Deploy Now, needs to configure the root .htaccess for Apache rewrite to index.php, write .deploy-now/<project>/config.yaml, or troubleshoot F3 routing on Deploy Now.
metadata:
  author: liusc45
  category: deployment
  workflow_version: v2
---

# Deploy Fat-Free Framework (F3) to IONOS Deploy Now

This skill helps deploy a Fat-Free Framework application to **IONOS Deploy Now**.

> **Disclaimer.** F3 is not officially detected by Deploy Now. Configured as a generic PHP project — **reasonable inference**.

## When to use

- "deploy Fat-Free to IONOS"
- "Deploy Now F3 setup"
- "Fat-Free Framework Deploy Now"

## Key facts

1. **Deploy Now generates the workflow.** Customize it, do not replace it.
2. **F3 is flexible on directory structure.** The only requirement: `index.php`, `.htaccess`, and public assets (CSS/JS/images) must be in the web-accessible directory.
3. **Suggested structure:** put `index.php` and `.htaccess` in the repo root; set publish directory to `./` (root).
4. Protect `app/`, `dict/`, `lib/`, `tmp/` directories via `.htaccess` rewrite rules.
5. v2 placeholders: `$IONOS_DB_HOST`, `$IONOS_APP_URL`.

## Workflow

1. ionos.space → new PHP project → connect repo.
2. Publish directory: **`./`** (root — F3 has no standard public/ subfolder).
3. Build: `composer install --no-dev --optimize-autoloader` (if using Composer).
4. Provision MariaDB (if using a DB).
5. Pull generated workflow → customize.
6. Add `.deploy-now/<project>/config.yaml`, `.env`, `.htaccess.template`.
7. Push.

## Validation checklist

| # | Check |
|---|---|
| 1 | `index.php` exists at repo root |
| 2 | `.htaccess` blocks `app/`, `dict/`, `lib/`, `tmp/`, `*.ini` |
| 3 | PHP ≥ 8.1 |
| 4 | `.gitignore` excludes `tmp/`, `.env` |
| 5 | `.deploy-now/<project>/config.yaml` excludes `tmp/` |

## Post-deploy commands

```yaml
post-deployment-remote-commands:
  - echo "F3 deployment complete"
```

## References

- `templates/deploy-now-config-v2.yaml`
- `templates/env.template`
- `templates/htaccess.template`
- F3 docs: https://fatfreeframework.com/3.9/routing-engine
