---
name: deploy-now-laravel
description: Deploy Laravel applications to IONOS Deploy Now. Laravel is officially auto-detected by Deploy Now per docs.ionos.space/docs/deploy-php-apps. Use when the user wants to deploy Laravel 10, 11, or 12 to Deploy Now, customize the auto-generated GitHub Actions workflow, configure .deploy-now/<project>/.env.template with $IONOS_DB_*, $IONOS_MAIL_*, and $IONOS_APP_URL placeholders, add post-deploy remote commands such as "php artisan migrate --force", "php artisan config:cache", "php artisan route:cache", "php artisan view:cache", "php artisan storage:link", set up cron jobs for Laravel scheduler, or fork the official ionos-deploy-now/laravel-starter as a reference.
metadata:
  author: liusc45
  category: deployment
  workflow_version: v2
---

# Deploy Laravel to IONOS Deploy Now

This skill helps an AI coding agent ship a Laravel application to **IONOS Deploy Now**.

> Laravel is **officially supported and auto-detected** by Deploy Now. The platform suggests build steps for Laravel automatically. The official starter is at https://github.com/ionos-deploy-now/laravel-starter — fork it for a fully prefilled setup.

## When to use this skill

Trigger on phrases such as:
- "deploy Laravel to IONOS"
- "Deploy Now Laravel setup"
- "ionos.space Laravel"
- "configure `.env.template` for Laravel on Deploy Now"
- "Laravel scheduler cron Deploy Now"
- "post-deploy artisan commands"

## Critical facts the agent must respect

1. **Deploy Now generates the GitHub Actions workflow.** Path under v2: `.github/workflows/<project>-build.yaml`. Do not write it from scratch — customize the generated file.
2. **Workflow v2 placeholder syntax** uses `$IONOS_DB_HOST`, `$IONOS_DB_NAME`, `$IONOS_DB_USERNAME`, `$IONOS_DB_PASSWORD`, `$IONOS_APP_URL`, `$IONOS_MAIL_HOST`, `$IONOS_MAIL_PORT`, `$IONOS_MAIL_USERNAME`, `$IONOS_MAIL_PASSWORD`, `$IONOS_MAIL_ENCRYPTION`, `$IONOS_MAIL_FROM_ADDRESS`.
3. **Publish directory must be `public/`** — Laravel's standard front controller location.
4. **`storage/` and `bootstrap/cache/` need persistence.** Add to `recurring.excludes` so per-deploy cleanup does not wipe sessions, logs, or user uploads.
5. **`APP_KEY` is required.** Generate locally with `php artisan key:generate --show`, store as a GitHub Actions secret, reference in `.env.template`.

## Workflow

### 1. Validate structure
Read `workflows/validate-structure.md`.

### 2. Guide through Deploy Now setup
1. Sign in at [ionos.space](https://ionos.space).
2. New PHP project → connect repo. Deploy Now detects Laravel automatically and pre-fills the wizard.
3. Confirm publish directory: **`public`**.
4. Confirm build commands suggested by Deploy Now (typically `composer install --no-dev --optimize-autoloader` plus `npm ci && npm run build` if Vite/Mix is used).
5. Provision MariaDB and send-mail account.
6. Set runtime PHP to 8.2 (or per project requirement).

### 3. Add a `APP_KEY` GitHub secret
Locally:
```
php artisan key:generate --show
```
Copy the resulting `base64:...` string into the repo's GitHub Actions secrets as `APP_KEY`. Then in the workflow customization, the `template-renderer-action` automatically passes secrets to the template renderer (workflow v2). No further wiring needed.

### 4. Customize the generated workflow
Read `workflows/customize-generated-workflow.md`. Pin PHP version, extensions, and any Vite/Mix steps.

### 5. Add runtime config
Copy these from `templates/`:
- `.deploy-now/<project>/config.yaml`
- `.deploy-now/<project>/.env.template`
- `.deploy-now/<project>/public/.htaccess.template`

### 6. Push
First push triggers deployment. Check the preview URL.

## Validation checklist

| # | Check | Action if failing |
|---|---|---|
| 1 | `artisan` exists at repo root | Confirm Laravel install |
| 2 | `composer.json` requires `laravel/framework` | Confirm version |
| 3 | `bootstrap/app.php` exists (any Laravel) | Confirm install |
| 4 | `composer.json` `require.php` ≥ 8.2 (Laravel 11/12 needs 8.2+) | Adjust |
| 5 | `.gitignore` excludes `vendor/`, `.env`, `node_modules/`, `storage/*.key`, `public/build`, `public/hot` | Add missing |
| 6 | `APP_KEY` secret added in GitHub Actions secrets | Add it |
| 7 | `.deploy-now/<project>/.env.template` uses `$IONOS_*` placeholders | Generate from template |
| 8 | `.deploy-now/<project>/config.yaml` excludes `storage/app/public/uploads` etc. | Generate from template |
| 9 | `public/.htaccess.template` present | Generate from template |
| 10 | Publish directory in dashboard = `public/` | Verify in Deploy Now dashboard |

## Post-deploy commands

The bundled `templates/deploy-now-config-v2.yaml` runs:

```yaml
post-deployment-remote-commands:
  - php8.2-cli artisan storage:link
  - php8.2-cli artisan migrate --force
  - php8.2-cli artisan config:cache
  - php8.2-cli artisan route:cache
  - php8.2-cli artisan view:cache
  - php8.2-cli artisan event:cache
```

## Bundled assets

- `workflows/validate-structure.md`
- `workflows/customize-generated-workflow.md`
- `templates/deploy-now-config-v2.yaml`
- `templates/env.template`
- `templates/htaccess.template` (root) — typically not needed if publish dir is `public/`
- `templates/public-htaccess.template`
- `references/laravel-starter-link.md`
- `references/post-deploy-recipes.md`
- `examples/laravel-walkthrough.md`
