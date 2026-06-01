# IONOS Deploy Now — overview

Source: https://docs.ionos.space/docs/deploy-php-apps/

## What it is

A managed CI/CD platform from IONOS that hooks into a GitHub repository and:

1. Generates a GitHub Actions workflow tailored to the detected stack.
2. Builds the project on every push using GitHub-hosted runners.
3. Deploys the build to IONOS shared hosting.
4. Provides a MariaDB database, send-mail account, cron job runner, custom-domain attachment, and automatic SSL.

## Officially supported PHP frameworks

Per the docs page above, **Deploy Now auto-detects only Laravel and Symfony**. Any other PHP application is treated as a generic PHP project. CodeIgniter 4 falls in this category.

## Workflow versions

| Version | Created | Workflow file path | Runtime config path |
|---|---|---|---|
| **v1** | Until 11/2022 | `.github/workflows/deploy-now.yaml` | `.deploy-now/config.yaml` |
| **v2** | From 11/2022 onward | `.github/workflows/<project>-build.yaml` | `.deploy-now/<project>/config.yaml` |

This skill targets **v2** unless explicitly told otherwise.

## Build pipeline

Build commands run inside GitHub Actions on a GitHub-hosted runner. They can use Composer, Node, Bundler, or any tool you can `apt install`. Source: https://docs.ionos.space/docs/github-actions-customization/

## Deployment

After a successful build the action `ionos-deploy-now/deploy-to-ionos-action` (Docker image `ghcr.io/ionos-deploy-now/deploy-to-ionos:v2.1.0`) uploads the build artifact via SSH to the IONOS webspace. **This action's inputs are wired by Deploy Now itself** and cannot be supplied by hand — there are required inputs for `service-host`, `deployment-id`, `deployment-info`, `ssh-user`, `ssh-key` that are derived from the dashboard setup.

## Runtime configuration

Path under v2: `.deploy-now/<project>/`. Files supported:

- `config.yaml` — file persistency excludes, pre/post-deploy remote commands, cron jobs.
- `*.template` — any application config file you want to render with secrets at deploy time. Example: `.env.template`, `.htaccess.template`. The path under `.deploy-now/<project>/` mirrors the destination path on the runtime (so `.deploy-now/<project>/public/.htaccess.template` becomes `public/.htaccess` after rendering).

Source: https://docs.ionos.space/docs/runtime-configuration/

## Placeholder syntax

| Workflow | Syntax | Example |
|---|---|---|
| v1 | Go-template | `{{ .runtime.db.host }}` |
| v2 | Shell-style | `$IONOS_DB_HOST` |

Custom secrets you add in GitHub Actions must also be referenced in the workflow's `template-renderer-action` step (v1 only).

## Limitations

- No SSH access to the runtime.
- No persistent server processes (no queue workers as long-running daemons; use cron jobs instead).
- Database backups are automatic, daily, retained 7 days; restore via Deploy Now support.
- Webspace storage: 10 GB.
