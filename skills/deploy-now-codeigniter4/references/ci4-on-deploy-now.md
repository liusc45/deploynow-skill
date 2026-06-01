# CodeIgniter 4 on IONOS Deploy Now — practical notes

> **Status: reasonable inference.** Deploy Now does not officially list CI4 as a supported framework. The notes below are derived from Deploy Now's generic PHP support combined with CI4's published deployment requirements (https://codeigniter4.github.io/userguide/installation/deployments.html).

## Project setup at ionos.space

1. Project type: **PHP project**.
2. Publish directory: **`public`**.
3. Build dependencies: **Composer**. Add **Node.js** only if the project builds front-end assets.
4. Build commands (replace the auto-suggestion):
   ```
   composer install --no-dev --optimize-autoloader --no-interaction --prefer-dist
   ```
   Plus, if applicable: `npm ci && npm run build`.
5. Runtime PHP version: **8.2** (recommended; 8.1 and 8.3 also supported).
6. Provision the bundled MariaDB.

## What the agent must always check

### Document root layout
CI4 expects `public/` to be the document root. Deploy Now's runtime serves the publish directory you configured in the dashboard (`public/`). The other CI4 directories — `app/`, `system/`, `writable/`, `vendor/` — are siblings of `public/` and stay outside the served path. Never set the publish directory to the repo root.

### `.env` lives next to `spark`
After deployment the rendered `.env` ends up in `$HOME/htdocs/.env`. CI4 reads it via `Config\DotEnv` from the same directory as `spark`.

### `writable/` permissions
Deploy Now hosts on shared infrastructure with sane defaults (typically `0755`/`0644`). CI4 writes to `writable/cache/`, `writable/logs/`, `writable/session/`, `writable/uploads/`. If the deployment viewer reports write errors, file an issue with Deploy Now support — do not try to `chmod` via remote commands (those run as the deploy user, not the webserver user).

### Persistence across deploys
By default Deploy Now overwrites the entire deployment folder on every push. Files generated at runtime (uploads, log files, session files) are wiped unless listed under `recurring.excludes` in `config.yaml`. The bundled `deploy-now-config-v2.yaml` template already excludes the right paths.

### Migrations
CI4 ships `php spark migrate --all`. Two options:

- **Manual.** Leave the migrate command commented out in `post-deployment-remote-commands`; run migrations on demand via the command line locally against the production DB.
- **Automatic.** Uncomment the line. Safe only if your migrations are idempotent and reversible. Most teams run migrations manually for production.

### Encryption key
CI4's `Config\Encryption` requires `encryption.key`. Generate locally with `php spark key:generate --show`, store in GitHub Actions secrets as `ENCRYPTION_KEY`, then reference in `.env.template` as `encryption.key = hex2bin:${ENCRYPTION_KEY}`. Add the secret to the workflow's `template-renderer-action` step (v1) — under v2 secrets are auto-passed.

### Sessions
Default file driver is fine. If you want database sessions: `session.driver = 'CodeIgniter\Session\Handlers\DatabaseHandler'` plus the session table from `vendor/codeigniter4/framework/system/Session/Handlers/Database/`.

### Logs and debugbar in production
Set `logger.threshold = 4` (warnings and above) and ensure `debug = false` (this is implicit when `CI_ENVIRONMENT = production`). Exclude `writable/debugbar/*` from `recurring.excludes` so debugbar artifacts do not pile up.

## Optional integrations

### Shield (auth)
No extra Deploy Now config needed. Run `php spark shield:setup` once locally before the first push to publish migrations and config.

### Queues
CI4 has no built-in queue system. If using `codeigniter4/tasks` or a third-party queue, configure it as a cron job in `config.yaml` rather than a long-running process.

### Caching
File cache is the default and works fine. For Redis you would need a managed Redis — Deploy Now does not provide one; use an external service.
