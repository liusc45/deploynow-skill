---
name: deploy-now-codeigniter4
description: Deploy CodeIgniter 4 applications to IONOS Deploy Now. CodeIgniter is NOT officially detected by Deploy Now (only Laravel and Symfony are auto-detected per docs.ionos.space/docs/deploy-php-apps), so this skill configures CI4 as a generic PHP project with Composer and optional Node build. Use when the user wants to deploy a CodeIgniter 4 app to IONOS Deploy Now, customize the auto-generated GitHub Actions workflow, write .deploy-now/<project>/config.yaml, configure .env.template and .htaccess.template, set the publish directory to public/, add post-deployment remote commands such as "php spark cache:clear" or "php spark migrate", or troubleshoot 403 errors, MariaDB connection issues, and writable/ permissions on Deploy Now.
metadata:
  author: liusc45
  category: deployment
  workflow_version: v2
---

# Deploy CodeIgniter 4 to IONOS Deploy Now

This skill helps an AI coding agent set up a CodeIgniter 4 project for deployment on **IONOS Deploy Now**.

> **Disclaimer.** Deploy Now officially auto-detects Laravel and Symfony only. CodeIgniter 4 is **not** in the official supported-framework list (see `references/deploy-now-overview.md`). The instructions below configure CI4 as a **generic PHP project** with Composer + optional Node build. Treat anything CI4-specific in this skill as **reasonable inference** consistent with Deploy Now's generic PHP support, not as documented vendor support.

## When to use this skill

Trigger on phrases such as:
- "deploy CodeIgniter 4 to IONOS"
- "Deploy Now CI4 setup"
- "ionos.space CodeIgniter"
- "configure `.deploy-now/config.yaml` for CodeIgniter"
- "CodeIgniter `.env.template` Deploy Now"
- "CI4 `writable/` persistence on Deploy Now"
- "403 error CodeIgniter Deploy Now"

## Critical facts the agent must respect

1. **Deploy Now generates the GitHub Actions workflow itself** when the user connects the repo at [ionos.space](https://ionos.space). Never instruct the user to write `.github/workflows/<project>-build.yaml` from scratch — instruct them to **let Deploy Now generate it, then customize**.
2. **Workflow v2** is the current default. Path is `.github/workflows/<project>-build.yaml` and runtime config lives at `.deploy-now/<project>/`. Workflow v1 paths differ (see `references/workflow-v1-vs-v2.md`).
3. **Action `ionos-deploy-now/deploy-to-ionos-action` cannot be wired manually.** Its required inputs (`service-host`, `deployment-id`, `deployment-info`, `ssh-user`, `ssh-key`) are injected by Deploy Now. Do not produce a workflow that calls this action directly.
4. **Placeholder syntax under workflow v2** uses shell-style variables: `$IONOS_DB_HOST`, `$IONOS_DB_NAME`, `$IONOS_DB_USERNAME`, `$IONOS_DB_PASSWORD`, `$IONOS_APP_URL`, `$IONOS_MAIL_*`. Workflow v1 uses Go-template syntax `{{ .runtime.db.host }}`.
5. **Publish directory must be `public/`** for CodeIgniter 4. The CI4 application code (`app/`, `system/`, `writable/`) lives outside the document root.

## Workflow

Follow these steps in order.

### 1. Detect framework
Read `workflows/detect-framework.md`. Confirm CodeIgniter 4 with at least 2 strong signals.

### 2. Validate structure
Read `workflows/validate-structure.md`. Run the checklist; report any failures.

### 3. Guide the user through Deploy Now setup
1. Sign in or sign up at [ionos.space](https://ionos.space/sign-up).
2. Create a new **PHP project** (not a static site).
3. Connect the GitHub repository.
4. When prompted for the **publish directory**, set it to `public`.
5. Adjust the suggested build steps (Composer; optional Node).
6. Set the runtime PHP version to `8.2` (or `8.1`/`8.3` per user preference).
7. Provision the bundled MariaDB.

### 4. Wait for Deploy Now to generate the workflow
After the wizard finishes, Deploy Now commits `.github/workflows/<project>-build.yaml` to the repo. Pull the latest changes locally before continuing.

### 5. Customize the generated workflow
Read `workflows/customize-generated-workflow.md`. Apply the snippets from `templates/workflow-customization.md` to the generated file (PHP version pin, extensions, composer flags).

### 6. Add the runtime configuration files
Create the following in the user's repo:
- `.deploy-now/<project>/config.yaml` — copy from `templates/deploy-now-config-v2.yaml`
- `.deploy-now/<project>/.env.template` — copy from `templates/env.template`
- `.deploy-now/<project>/.htaccess.template` — copy from `templates/htaccess.template`
- `.deploy-now/<project>/public/.htaccess.template` — copy from `templates/public-htaccess.template`

Replace `<project>` with the actual project slug shown in the Deploy Now dashboard.

### 7. Push and verify
The user pushes; the generated workflow runs; Deploy Now publishes the build to a preview URL (`*.ionos.space`). The agent should verify the homepage, a dynamic route, the database connection, and writes to `writable/`.

## Validation checklist

| # | Check | Action if failing |
|---|---|---|
| 1 | `public/index.php` exists | Block deployment; CI4 install incomplete |
| 2 | `composer.json` requires `codeigniter4/framework` | Confirm framework detection |
| 3 | `composer.json` `require.php` ≥ 8.1 | Warn; recommend bump |
| 4 | `.gitignore` excludes `writable/cache/*`, `writable/logs/*`, `writable/session/*`, `vendor/`, `.env` | Add missing entries |
| 5 | `app/Config/App.php` lets `app.baseURL` be overridden via env | Adjust if hardcoded |
| 6 | `writable/` exists with all subdirectories | Recreate with `.gitkeep` files |
| 7 | `.deploy-now/<project>/config.yaml` exists | Generate from template |
| 8 | `.deploy-now/<project>/.env.template` exists with `$IONOS_DB_*` placeholders | Generate from template |
| 9 | `.deploy-now/<project>/.htaccess.template` exists | Generate from template |
| 10 | Publish directory in dashboard = `public` | Fix in Deploy Now settings |

## Common errors → solution

See `references/common-errors.md` for the full table. Top three:

| Error | Cause | Fix |
|---|---|---|
| `403 Forbidden` on root URL | Publish directory not set to `public/` | In Deploy Now dashboard, set publish dir to `public` and redeploy |
| `Unable to connect to your database server` | `.env.template` placeholders missing or wrong syntax | Use `$IONOS_DB_HOST` (v2) or `{{ .runtime.db.host }}` (v1) |
| `404` on every route except `/` | Apache rewrites missing | Ensure `.htaccess.template` and `public/.htaccess.template` are present and correct |

## Output format the agent should produce

When invoked, produce:

1. **Diagnosis** — framework + confidence level (high/medium/low).
2. **Validation checklist** — table with pass/fail for all 10 checks.
3. **Generated files** — full content of each template adapted to the user's project.
4. **Deployment steps** — numbered list specific to the user's repo and project name.
5. **Post-deploy verification** — commands and URLs to test.
6. **Troubleshooting reference** — link the agent to `references/common-errors.md` for any failures encountered.

## Bundled assets

- `workflows/detect-framework.md` — detection rules
- `workflows/validate-structure.md` — pre-deploy checklist
- `workflows/customize-generated-workflow.md` — how to edit Deploy Now's generated YAML
- `workflows/troubleshoot.md` — diagnostic flow
- `templates/deploy-now-config-v2.yaml`
- `templates/env.template`
- `templates/htaccess.template`
- `templates/public-htaccess.template`
- `templates/workflow-customization.md`
- `references/deploy-now-overview.md`
- `references/ci4-on-deploy-now.md`
- `references/workflow-v1-vs-v2.md`
- `references/htaccess-recipes.md`
- `references/common-errors.md`
- `references/domain-ssl.md`
- `examples/ci4-realistic-walkthrough.md`
