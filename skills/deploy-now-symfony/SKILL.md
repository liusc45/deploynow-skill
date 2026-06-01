---
name: deploy-now-symfony
description: Deploy Symfony applications to IONOS Deploy Now. Symfony is officially
  auto-detected by Deploy Now per docs.ionos.space/docs/deploy-php-apps. Use when the
  user wants to deploy Symfony 6 or 7 to Deploy Now, customize the auto-generated
  GitHub Actions workflow, configure .deploy-now/<project>/.env.template with
  $IONOS_DB_* and $IONOS_MAIL_* placeholders, add post-deploy remote commands such as
  "bin/console cache:clear --env=prod", "bin/console doctrine:migrations:migrate
  --no-interaction", "bin/console asset-map:compile", set up cron jobs, or fork the
  official ionos-deploy-now/symfony-starter as a reference.
metadata:
  author: liusc45
  category: deployment
  workflow_version: v2
---

# Deploy Symfony to IONOS Deploy Now

This skill helps an AI coding agent ship a Symfony application to **IONOS Deploy Now**.

> Symfony is **officially supported and auto-detected** by Deploy Now. The platform suggests build steps for Symfony automatically. The official starter is at https://github.com/ionos-deploy-now/symfony-starter.

## When to use this skill

Trigger on phrases such as:
- "deploy Symfony to IONOS"
- "Deploy Now Symfony setup"
- "ionos.space Symfony"
- "configure `.env.template` for Symfony on Deploy Now"
- "Symfony post-deploy cache warmup Deploy Now"
- "Doctrine migrations Deploy Now"

## Critical facts the agent must respect

1. **Deploy Now generates the GitHub Actions workflow.** Path under v2: `.github/workflows/<project>-build.yaml`. Customize the generated file, do not replace it.
2. **Workflow v2 placeholder syntax** uses `$IONOS_DB_HOST`, `$IONOS_DB_NAME`, `$IONOS_DB_USERNAME`, `$IONOS_DB_PASSWORD`, `$IONOS_APP_URL`, `$IONOS_MAIL_*`.
3. **Publish directory must be `public/`** — Symfony's standard front controller location.
4. **`var/` needs persistence** (cache, logs, sessions). Add to `recurring.excludes`.
5. **`APP_SECRET` is required.** Generate locally with `php -r "echo bin2hex(random_bytes(16));"` and store as a GitHub Actions secret.

## Workflow

### 1. Validate structure
Read `workflows/validate-structure.md`.

### 2. Guide through Deploy Now setup
1. Sign in at [ionos.space](https://ionos.space).
2. New PHP project → connect repo. Deploy Now detects Symfony automatically.
3. Confirm publish directory: **`public`**.
4. Confirm build commands (Composer + optional Node/Webpack Encore).
5. Provision MariaDB and send-mail account.
6. Runtime PHP: 8.2.

### 3. Add `APP_SECRET` GitHub secret
Locally:
```
php -r "echo bin2hex(random_bytes(16));"
```
Add to repo Settings → Secrets and variables → Actions as `APP_SECRET`.

### 4. Customize the generated workflow
Read `workflows/customize-generated-workflow.md`.

### 5. Add runtime config
- `.deploy-now/<project>/config.yaml` — from `templates/deploy-now-config-v2.yaml`
- `.deploy-now/<project>/.env.template` — from `templates/env.template`
- `.deploy-now/<project>/public/.htaccess.template` — from `templates/public-htaccess.template`

### 6. Push

## Validation checklist

| # | Check | Action if failing |
|---|---|---|
| 1 | `bin/console` exists at repo root | Confirm Symfony install |
| 2 | `composer.json` requires `symfony/framework-bundle` | Confirm version |
| 3 | `config/bundles.php` exists | Confirm install |
| 4 | `composer.json` `require.php` ≥ 8.1 | Adjust |
| 5 | `.gitignore` excludes `vendor/`, `.env`, `var/`, `node_modules/`, `public/build/`, `public/bundles/` | Add missing |
| 6 | `APP_SECRET` GitHub Actions secret exists | Add it |
| 7 | `.deploy-now/<project>/.env.template` uses `$IONOS_*` | Generate from template |
| 8 | `.deploy-now/<project>/config.yaml` excludes `var/` | Generate from template |
| 9 | `public/.htaccess.template` present | Generate from template |
| 10 | Publish directory in dashboard = `public/` | Verify |

## Post-deploy commands

```yaml
post-deployment-remote-commands:
  - php8.2-cli bin/console cache:clear --env=prod
  - php8.2-cli bin/console doctrine:migrations:migrate --no-interaction
  - php8.2-cli bin/console asset-map:compile
```

## Bundled assets

- `workflows/validate-structure.md`
- `workflows/customize-generated-workflow.md`
- `templates/deploy-now-config-v2.yaml`
- `templates/env.template`
- `templates/public-htaccess.template`
- `references/symfony-starter-link.md`
- `references/post-deploy-recipes.md`
- `examples/symfony-walkthrough.md`
