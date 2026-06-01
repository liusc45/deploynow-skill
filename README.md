# deploynow-skill

> Deploy any PHP framework to **IONOS Deploy Now** with confidence.
> 12 framework skills. One CLI install. Zero vendor lock-in for the common cases.

[![skills: 12](https://img.shields.io/badge/skills-12-blue)](.)
[![workflow: v2](https://img.shields.io/badge/workflow-v2-brightgreen)](.)
[![license: MIT](https://img.shields.io/badge/license-MIT-green)](LICENSE)
[![author: liusc45](https://img.shields.io/badge/author-liusc45-orange)](.)

Works with [`skills`](https://www.npmjs.com/package/skills), the open agent skills CLI. Compatible with OpenCode, Claude Code, Cursor, Codex, Cline, and 50+ agents.

---

## Table of contents

1. [Quickstart (5 minutes)](#1-quickstart-5-minutes)
2. [Which skill do I need?](#2-which-skill-do-i-need)
3. [What you need before you start](#3-what-you-need-before-you-start)
4. [End-to-end walkthrough](#4-end-to-end-walkthrough-15--30-minutes)
5. [What each skill does](#5-what-each-skill-does)
6. [Catalog at a glance](#6-catalog-at-a-glance)
7. [Troubleshooting cookbook](#7-troubleshooting-cookbook)
8. [FAQ](#8-faq)
9. [Architecture: how Deploy Now actually works](#9-architecture-how-deploy-now-actually-works)
10. [Workflow v1 vs v2 — do I need to care?](#10-workflow-v1-vs-v2--do-i-need-to-care)
11. [Glossary](#11-glossary)
12. [Source of truth](#12-source-of-truth)
13. [Contributing](#13-contributing)
14. [License](#14-license)

---

## 1. Quickstart (5 minutes)

If your framework is Laravel or Symfony, the agent will do almost everything for you. For everything else, the agent walks you through a 15–30 minute setup.

```bash
# 1. List all available skills in this catalog
npx skills add liusc45/deploynow-skill --list

# 2. Install the skill for your framework (replace with your framework slug)
npx skills add liusc45/deploynow-skill --skill deploy-now-laravel

# 3. Open your agent and ask: "Deploy my Laravel app to IONOS Deploy Now"
#    The agent loads the skill and guides you step by step.
```

**That's it.** The skill itself does not touch your repo — it teaches your agent the correct Deploy Now workflow, configuration files, and commands for your specific framework.

### Slug cheatsheet

| Your framework | Skill slug |
|---|---|
| Laravel 10 / 11 / 12 | `deploy-now-laravel` |
| Symfony 6 / 7 | `deploy-now-symfony` |
| CodeIgniter 4 | `deploy-now-codeigniter4` |
| CakePHP 4 / 5 | `deploy-now-cakephp` |
| Yii2 | `deploy-now-yii` |
| Slim 4 | `deploy-now-slim` |
| Phalcon 5 | `deploy-now-phalcon` |
| Laminas MVC | `deploy-now-laminas` |
| Mezzio | `deploy-now-mezzio` |
| Fat-Free (F3) | `deploy-now-fatfree` |
| Flight | `deploy-now-flight` |
| Spiral | **`deploy-now-spiral`** — *incompatible, see below* |

---

## 2. Which skill do I need?

```
Are you using Laravel or Symfony?
├── Yes → Use deploy-now-laravel / deploy-now-symfony
│         (Deploy Now auto-detects these — minimal config)
│
└── No  → Does your framework ship its own long-running process server
          (Swoole, OpenSwoole, RoadRunner, FrankenPHP, ReactPHP)?
          ├── Yes, RoadRunner → deploy-now-spiral
          │                     (INCOMPATIBLE — Deploy Now is shared PHP-FPM)
          │
          ├── Yes, Swoole/OpenSwoole → No skill yet.
          │   PRs welcome. For now, run on Railway, Render, or a VPS.
          │
          └── No  → Pick the skill matching your framework from the table above.
                    Deploy Now treats it as a "generic PHP project".
```

**Quick visual:**

| Framework | Auto-detected? | Long-running process? | Skill exists? |
|---|---|---|---|
| Laravel | Yes | No | Yes — easiest path |
| Symfony | Yes | No | Yes — easiest path |
| CodeIgniter 4 | No | No | Yes |
| CakePHP 4/5 | No | No | Yes |
| Yii2 | No | No | Yes |
| Slim 4 | No | No | Yes |
| Phalcon 5 | No | No | Yes — needs C extension |
| Laminas MVC | No | No | Yes |
| Mezzio | No | No | Yes |
| Fat-Free | No | No | Yes |
| Flight | No | No | Yes |
| Spiral | No | **Yes (RoadRunner)** | Yes — **incompatible notice only** |

---

## 3. What you need before you start

Check this list before you ask the agent to deploy. Missing items cause 90% of first-time failures.

### Account & tools
- [ ] An [IONOS Deploy Now](https://ionos.space/sign-up) account (free tier is enough for a preview deploy)
- [ ] A GitHub account (Deploy Now connects via GitHub)
- [ ] Your PHP project in a **public** GitHub repository (private repos require a paid plan)
- [ ] Local clone of the repo with push access
- [ ] Node.js 20+ and `npx` available (for the `skills` CLI)

### Project requirements
- [ ] `composer.json` declares a `require.php` of **8.1 or higher** (8.2 recommended)
- [ ] `composer.lock` is committed (Deploy Now runs `composer install`)
- [ ] `.gitignore` excludes `vendor/`, `.env`, runtime caches, and uploaded files
- [ ] No custom system binaries required (Deploy Now is shared Linux + PHP-FPM)
- [ ] No background daemons, cron workers, or long-running PHP processes (except via `runtime.cron-jobs`)

### Framework-specific quick checks
- **Laravel**: `APP_KEY` generation must be in post-deploy, not build. `php artisan storage:link` required if you serve user uploads.
- **Symfony**: `APP_ENV=prod` and `APP_DEBUG=0` for production deploys. Run `cache:clear` after each deploy.
- **CodeIgniter 4**: `writable/` must exist and be writable at runtime. `public/` is the only doc root.
- **CakePHP**: `webroot/` is the only doc root. `tmp/` must be writable.
- **Yii2**: `web/` is the only doc root. `runtime/` and `assets/` must be writable.
- **Phalcon**: The `phalcon` C extension **must be available** on Deploy Now's runtime. Verify before starting.
- **Laminas / Mezzio**: Run `composer development-disable` after deploy to clear dev config cache.
- **Slim / Flight / Fat-Free**: Document root is `public/` (or repo root for F3). Front controller pattern requires `.htaccess` rewrite.

---

## 4. End-to-end walkthrough (15–30 minutes)

This is a realistic timeline. Time estimates are for a developer who has deployed PHP apps before; first-timers should budget 45–60 minutes.

### Phase 1: Prepare locally (5 minutes)

```bash
# 1. Verify your project is deploy-ready
composer validate --strict
php -v                 # confirm local PHP version matches what you'll target

# 2. Make sure git is clean
git status
git add -A
git commit -m "Prepare for Deploy Now"
git push
```

### Phase 2: Install the skill (1 minute)

```bash
npx skills add liusc45/deploynow-skill --skill deploy-now-<your-framework>
```

### Phase 3: Connect to Deploy Now (3 minutes)

1. Open [ionos.space](https://ionos.space) and sign in.
2. Click **New project** → **PHP** (not static site).
3. Authorize the GitHub App and select your repository.
4. When prompted for the **publish directory**, set the value from the table below.
5. Accept the suggested build steps. Don't change them yet — the agent will customize them in Phase 5.
6. Choose PHP **8.2** (or your version) as the runtime.
7. Click **Deploy**. The first build is a smoke test; it will likely fail because the workflow is generic.

| Framework | Publish directory |
|---|---|
| Laravel, Symfony, CodeIgniter 4, Slim, Laminas, Mezzio, Phalcon, Flight | `public` |
| CakePHP | `webroot` |
| Yii2 | `web` |
| Fat-Free | `.` (repo root) |

### Phase 4: Pull the generated workflow (1 minute)

Deploy Now committed `.github/workflows/<project>-build.yaml` to your repo.

```bash
git pull
ls .github/workflows/
```

### Phase 5: Ask the agent to finish the setup (10–20 minutes)

Open your agent (Claude Code, Cursor, OpenCode, etc.) and say:

> "I just connected my repository to IONOS Deploy Now. The auto-generated workflow is at `.github/workflows/<project>-build.yaml`. Apply the `deploy-now-<framework>` skill to finish configuration: generate `.deploy-now/<project>/config.yaml`, `.env.template`, and `.htaccess.template`, then customize the workflow for my framework."

The agent will:
1. Run the framework-detection checklist.
2. Run the project-structure validation.
3. Copy the bundled templates and adapt them to your project name.
4. Patch the generated workflow (PHP version, extensions, Composer flags, build steps).
5. Hand you a list of files to commit and push.

### Phase 6: Push and verify (3 minutes)

```bash
git add .deploy-now/ .github/workflows/
git commit -m "Deploy Now: configure <framework> runtime"
git push
```

Watch the Actions tab in GitHub. When the build goes green, Deploy Now issues a preview URL like `https://<project>-<id>.ionos.space`. Open it and verify:
- Homepage loads
- A dynamic route works (e.g., a list page that hits the database)
- Login / form submission writes to the database
- File uploads persist (if applicable)

### Phase 7: Go live (optional, 5 minutes)

1. In the Deploy Now dashboard, add your custom domain.
2. Deploy Now provisions a Let's Encrypt certificate automatically.
3. Update your DNS (CNAME or ALIAS) to point to the Deploy Now endpoint.
4. Wait for DNS propagation (5–60 minutes). SSL is issued once DNS resolves.

---

## 5. What each skill does

When you install a skill and ask your agent to use it, the agent will:

1. **Detect** the framework in your repo with explicit confidence signals.
2. **Validate** that your project structure is Deploy-Now-compatible (10-point checklist).
3. **Generate** the runtime configuration files in `.deploy-now/<project>/`:
   - `config.yaml` — what to exclude, what commands to run before/after deploy, cron jobs.
   - `.env.template` — environment variable placeholders (`$IONOS_DB_HOST`, etc.) that Deploy Now replaces at runtime.
   - `.htaccess.template` — Apache rewrite rules and security headers.
4. **Customize** the GitHub Actions workflow Deploy Now generated — never write one from scratch.
5. **Verify** the first deploy and diagnose common errors (403, 500, DB connection failures, routing).

The agent will **not** do any of these (and you should not either):
- ❌ Manually create `.github/workflows/<project>-build.yaml` from scratch.
- ❌ Hardcode the `ionos-deploy-now/deploy-to-ionos-action` step — its inputs are injected by Deploy Now.
- ❌ Use the v1 placeholder syntax `{{ .runtime.db.host }}` — that is deprecated.
- ❌ Mark the `writable/`, `storage/`, or `var/` directories as `bootstrap.excludes` (they need to be created at first deploy) — use `recurring.excludes` instead.

---

## 6. Catalog at a glance

| Skill | Framework | Versions | Publish dir | Document root | Skill confidence |
|---|---|---|---|---|---|
| `deploy-now-laravel` | Laravel | 10 / 11 / 12 | `public/` | repo root (Laravel) | High — auto-detected by Deploy Now |
| `deploy-now-symfony` | Symfony | 6 / 7 | `public/` | repo root (Symfony) | High — auto-detected by Deploy Now |
| `deploy-now-codeigniter4` | CodeIgniter | 4.x | `public/` | `public/` | High — `spark` CLI is the signature |
| `deploy-now-cakephp` | CakePHP | 4 / 5 | `webroot/` | `webroot/` | High — `bin/cake` CLI |
| `deploy-now-yii` | Yii2 | 2.x | `web/` | `web/` | High — `yii` CLI |
| `deploy-now-slim` | Slim | 4.x | `public/` | `public/` | High — front controller pattern |
| `deploy-now-phalcon` | Phalcon | 5.x | `public/` | `public/` | **Medium — requires C extension** |
| `deploy-now-laminas` | Laminas MVC | 3.x | `public/` | `public/` | High |
| `deploy-now-mezzio` | Mezzio | 3.x / 4.x | `public/` | `public/` | High |
| `deploy-now-fatfree` | Fat-Free (F3) | 3.x | `./` (root) | repo root | High |
| `deploy-now-flight` | Flight | 2.x / 3.x | `public/` or root | `public/` | High |
| `deploy-now-spiral` | Spiral | 3.x | N/A | N/A | **Incompatible — see below** |

### Spiral: why it doesn't work

Spiral is designed to run on **RoadRunner**, a long-running PHP application server. Deploy Now provides **PHP-FPM only** — there is no way to keep a long-running process alive between requests. The `deploy-now-spiral` skill exists only to document the incompatibility and recommend alternatives:

- **Railway** — `$5/mo`, supports RoadRunner natively, GitHub deploys.
- **Hetzner Cloud** — €4/mo VPS, full control, you run RoadRunner yourself.
- **DigitalOcean App Platform** — supports Docker, you ship your own RoadRunner image.
- **Laravel Forge** — if you want a managed server with Forge's dashboard.

---

## 7. Troubleshooting cookbook

These are the top 10 errors and the one-line fix. Each skill also bundles a longer `references/common-errors.md` with full diagnosis.

| Symptom | Cause | Fix |
|---|---|---|
| `403 Forbidden` on `/` | Publish directory not set in dashboard | Set publish dir in Deploy Now dashboard to `public` (or framework-specific) |
| `500` immediately after deploy | `.env` placeholders not resolved | Use `$IONOS_DB_HOST` (v2), not `{{ .runtime.db.host }}` (v1) |
| `Unable to connect to your database server` | `env()` not reading from runtime | Check `.deploy-now/<project>/.env.template` exists and is committed |
| Homepage loads but every other route is `404` | Apache rewrites missing | Ensure `.htaccess.template` is in the doc root, not repo root |
| `Class 'XYZ' not found` after deploy | Composer `--optimize-autoloader` not running | Add `--optimize-autoloader` to the workflow's composer step |
| Build succeeds but app uses old code | `bootstrap.cache/` not excluded | Add `bootstrap/cache` to `recurring.excludes` (Laravel) |
| `Permission denied` writing to `writable/` (CI4) | Directory not created at first deploy | Add `writable/cache, writable/logs, writable/session` to `recurring.excludes` and let Deploy Now create them |
| `Phalcon extension not loaded` | C extension missing from runtime | Switch to a different framework or platform |
| Cron jobs not running | `runtime.cron-jobs` syntax wrong | Use `command: php8.2-cli $HOME/htdocs/<path>` and a valid cron schedule |
| `Mixed content` warnings | `app.baseURL` starts with `http://` | Set `app.baseURL` from `$IONOS_APP_URL` (already HTTPS) |

### Where to get more help

1. The skill's `references/common-errors.md` — framework-specific deep dive.
2. The skill's `examples/<framework>-realistic-walkthrough.md` — a worked example from detection to live URL.
3. [docs.ionos.space](https://docs.ionos.space/) — official Deploy Now docs.
4. The framework's own docs — the skill is built on top of them, not in place of them.

---

## 8. FAQ

### Why isn't my framework auto-detected by Deploy Now?

Only **Laravel** and **Symfony** are in the official auto-detect list. See the [deploy-php-apps docs](https://docs.ionos.space/docs/deploy-php-apps/). The IONOS team has not published a detection extension API, so the catalog configures other frameworks as "generic PHP project" with framework-specific config files. This works for every framework that follows the standard `public/` (or framework-equivalent) doc-root pattern.

### Can I use a private GitHub repo?

Only on a paid Deploy Now plan. The free tier requires public repos.

### Can I use GitLab or Bitbucket?

No. Deploy Now integrates with GitHub only.

### How long does a deploy take?

| Phase | Time |
|---|---|
| First build (after repo connect) | 2–4 minutes |
| First build (after agent customization) | 2–4 minutes |
| Subsequent builds | 30–90 seconds |
| Cold start (first request after long idle) | <1 second |
| Warm request | <100ms |

### Where do I set environment variables that aren't `$IONOS_*` placeholders?

Use GitHub repository **Settings → Secrets and variables → Actions → Variables** (or Secrets for sensitive values). Reference them in the workflow file as `${{ vars.MY_VAR }}` or `${{ secrets.MY_SECRET }}`. Note: these are exposed at **build** time, not runtime. For runtime-only values, add them to the Deploy Now dashboard's environment variables.

### How do I run database migrations?

Add the migration command to `post-deployment-remote-commands` in `.deploy-now/<project>/config.yaml`:

- Laravel: `php8.2-cli artisan migrate --force --no-interaction`
- Symfony: `php8.2-cli bin/console doctrine:migrations:migrate --no-interaction`
- CodeIgniter 4: `php8.2-cli spark migrate --all`
- CakePHP: `php8.2-cli bin/cake migrations migrate`
- Yii2: `php8.2-cli yii migrate --interactive=0`

### How do I run a Laravel scheduler (cron)?

```yaml
# .deploy-now/<project>/config.yaml
runtime:
  cron-jobs:
    - command: php8.2-cli $HOME/htdocs/artisan schedule:run
      schedule: '* * * * *'
```

The Laravel scheduler runs every minute; your task definitions control the actual frequency.

### How do I add a custom domain?

In the Deploy Now dashboard, open the project → **Settings → Domains → Add custom domain**. Enter the domain; Deploy Now shows you the DNS records to add. Once DNS resolves, Deploy Now issues a Let's Encrypt certificate automatically.

### How do I roll back a bad deploy?

In the Deploy Now dashboard, open the project → **Deployments → click the green "..." on a previous successful deployment → Redeploy**. The previous build artifact is restored within 60 seconds.

### How do I add a second environment (staging)?

Click **New project** in the dashboard and connect the same repo but a different branch (e.g., `develop`). Each environment is a separate project with its own URL, database, and env vars.

### Is Deploy Now HIPAA / SOC2 / PCI compliant?

This skill cannot answer that. Check [IONOS's compliance page](https://www.ionos.com/compliance) for the current certifications. For most apps, Deploy Now runs on shared infrastructure in IONOS data centers in Germany.

### Why does the agent sometimes say "reasonable inference"?

For frameworks that Deploy Now does not officially support, the skill bundles config files and commands based on the framework's own documentation. The skill explicitly labels this as inference, not vendor support. If Deploy Now changes its generic-PHP behavior, the skill may need updates — please open an issue.

---

## 9. Architecture: how Deploy Now actually works

```
┌──────────────────┐     ┌──────────────────────┐     ┌──────────────────────┐
│  Your machine    │     │   GitHub             │     │  IONOS Deploy Now    │
│                  │     │                      │     │                      │
│  - Write code    │────▶│  - Receives push     │────▶│  - Triggered by push │
│  - Install skill │     │  - Runs workflow     │     │  - Builds artifact   │
│  - Ask agent     │     │  - Uploads to        │     │  - Provisions DB     │
│                  │     │    Deploy Now        │     │  - Sets up cron      │
└──────────────────┘     └──────────────────────┘     └──────────────────────┘
                                                              │
                                                              ▼
                                                     ┌──────────────────────┐
                                                     │  Live URL            │
                                                     │  https://<id>.ionos  │
                                                     │  .space              │
                                                     │  (or your custom     │
                                                     │   domain)            │
                                                     └──────────────────────┘
```

What Deploy Now manages for you:
- **Build environment** — Ubuntu + PHP 8.1/8.2/8.3 + Composer + Node 18/20 + your project's tooling
- **Runtime** — Apache + PHP-FPM, configurable PHP version
- **Database** — bundled MariaDB, credentials injected as `$IONOS_DB_*` env vars
- **Cron** — declarative `runtime.cron-jobs` in `config.yaml`
- **TLS** — automatic Let's Encrypt for custom domains
- **Preview URLs** — every successful build gets `*.ionos.space`
- **Rollback** — previous builds are retained and redeployable

What you manage:
- Your application code
- Your framework's `composer.json` / `package.json`
- The framework-specific config in `.deploy-now/<project>/`

What you **cannot** do on Deploy Now:
- Install system packages (no `apt install`)
- Run a long-lived background process (use cron jobs instead)
- SSH into the runtime (debug via logs and the preview URL)
- Mount a persistent filesystem other than Deploy Now's own (no NFS, no S3-FUSE)

---

## 10. Workflow v1 vs v2 — do I need to care?

**No, if your project was created after November 2022.** You are on v2 by default. Skip this section.

**Yes, if your project was created earlier.** Workflow v1 differs in two important ways:

| Aspect | v1 (legacy) | v2 (current) |
|---|---|---|
| Placeholder syntax | Go template: `{{ .runtime.db.host }}` | Shell variable: `$IONOS_DB_HOST` |
| Path to workflow | `.deploy-now/workflows/<name>.yaml` | `.github/workflows/<project>-build.yaml` |
| Path to runtime config | `.deploy-now/runtime.yml` | `.deploy-now/<project>/config.yaml` |
| Action | Custom Deploy Now action | `ionos-deploy-now/deploy-to-ionos-action@v1` |

If you have a v1 project, the [CodeIgniter4 skill's `references/workflow-v1-vs-v2.md`](../blob/main/skills/deploy-now-codeigniter4/references/workflow-v1-vs-v2.md) has a detailed migration guide.

---

## 11. Glossary

- **Deploy Now** — IONOS's PaaS product for hosting PHP, Node, and static sites. Built on GitHub Actions and custom IONOS infrastructure.
- **ionos.space** — The free preview URL domain that Deploy Now assigns to every successful build. Custom domains replace this.
- **Auto-detected framework** — A framework whose presence Deploy Now can identify from your repo (currently only Laravel and Symfony). Auto-detected frameworks get optimized build steps by default.
- **Generic PHP project** — A non-auto-detected framework that Deploy Now treats as a standard PHP application. You supply the build steps and configuration.
- **Workflow** — The GitHub Actions YAML file Deploy Now generates at `.github/workflows/<project>-build.yaml`. It runs on every push.
- **Runtime** — The Apache + PHP-FPM environment that serves your application. Distinct from the build environment.
- **Publish directory** — The directory in your repo that the runtime serves as the document root. Framework-specific (see table in Phase 3).
- **Placeholder** — A shell-style variable like `$IONOS_DB_HOST` in your `.env.template` that Deploy Now replaces with the actual value at runtime.
- **Recurring exclude** — A path in `recurring.excludes` that Deploy Now preserves across deploys (your uploads, your logs, your runtime cache). Files in `bootstrap.excludes` are excluded from the upload entirely.
- **Post-deployment remote command** — A shell command that Deploy Now runs on the IONOS runtime after each deploy (migrations, cache clear, storage link).
- **Cron job** — A scheduled command defined in `runtime.cron-jobs`. Deploy Now manages the crontab for you.
- **RoadRunner** — A long-running PHP application server written in Go. Used by Spiral and a few other frameworks. Incompatible with Deploy Now's PHP-FPM model.
- **Skill** — A directory under `skills/<name>/SKILL.md` that teaches an AI agent how to perform a specific task. The `skills` CLI distributes them.

---

## 12. Source of truth

Every claim in this catalog was verified against the official IONOS Deploy Now documentation and each framework's authoritative docs:

- [docs.ionos.space](https://docs.ionos.space/) — Deploy Now home
- [docs.ionos.space/docs/deploy-php-apps/](https://docs.ionos.space/docs/deploy-php-apps/) — framework support matrix
- [docs.ionos.space/docs/runtime-configuration/](https://docs.ionos.space/docs/runtime-configuration/) — `config.yaml` reference
- [docs.ionos.space/docs/deployment-configuration/](https://docs.ionos.space/docs/deployment-configuration/) — `excludes` reference
- [docs.ionos.space/docs/github-actions-customization/](https://docs.ionos.space/docs/github-actions-customization/) — workflow editing
- [docs.ionos.space/docs/cronjobs/](https://docs.ionos.space/docs/cronjobs/) — cron syntax
- [docs.ionos.space/docs/apache-configuration-htaccess/](https://docs.ionos.space/docs/apache-configuration-htaccess/) — `.htaccess` patterns
- [github.com/ionos-deploy-now/laravel-starter](https://github.com/ionos-deploy-now/laravel-starter) — Laravel reference deploy
- [github.com/ionos-deploy-now/symfony-starter](https://github.com/ionos-deploy-now/symfony-starter) — Symfony reference deploy

For each framework, the skill also cites the official framework docs (CakePHP book, Yii2 guide, Slim docs, etc.).

> **Important.** Deploy Now **generates** the GitHub Actions workflow when you connect the repository. You do **not** write it from scratch. The skill teaches your agent to **customize** the generated file, not to author a new one.

---

## 13. Contributing

Contributions are welcome for:
- New framework skills (Swoole, OpenSwoole, ReactPHP, FrankenPHP, etc.)
- Better diagnostic rules in `references/common-errors.md`
- Verified examples for edge cases (multi-site Laravel, monorepo, etc.)
- Translations (the catalog is English-only by design)

See [`docs/CONTRIBUTING.md`](docs/CONTRIBUTING.md) for the workflow, style guide, and validation rules.

---

## 14. License

[MIT](LICENSE) © liusc45
