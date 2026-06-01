# Troubleshoot CodeIgniter 4 deployments

Use this flow when the user reports a failure.

## Diagnostic order

1. **Check the GitHub Actions run.** Open the latest run on `.github/workflows/<project>-build.yaml`. Read the failing step.
2. **Check Deploy Now logs.** Dashboard → project → Deployment viewer → log tab.
3. **Check the live URL** with the browser dev tools network tab — note the response status code.

## Decision tree

### 403 Forbidden on the root URL
- Publish directory in dashboard is wrong. Set to `public`.
- Or: `public/index.php` is missing in the deployed bundle.

### 404 on every route except `/`
- `.htaccess` not deployed or `mod_rewrite` rule missing.
- Verify both `.deploy-now/<project>/.htaccess.template` and `.deploy-now/<project>/public/.htaccess.template` exist.

### 500 Internal Server Error
1. View `writable/logs/log-YYYY-MM-DD.php` via the deployment viewer.
2. Common causes:
   - `.env` was not rendered → check that `.env.template` syntax is correct.
   - `writable/` is not writable → CI4 needs write access. Confirm with deployment viewer.
   - `encryption.key` missing → set in `.env.template` or via secrets.

### Database connection refused
- Ensure `.env.template` uses `$IONOS_DB_HOST`, `$IONOS_DB_NAME`, `$IONOS_DB_USERNAME`, `$IONOS_DB_PASSWORD` (workflow v2).
- Confirm the MariaDB was provisioned in the Deploy Now wizard.
- `database.default.DBDriver = MySQLi` and `port = 3306`.

### Deployment succeeded but uploads disappear
- File persistency missing. Add `public/uploads` and `writable/uploads` under `recurring.excludes` in `.deploy-now/<project>/config.yaml`.

### `composer install` fails
- Lock file out of sync with `composer.json` → run `composer update --lock` locally and commit `composer.lock`.
- Required PHP extension missing → add it under `extensions:` in the `Setup PHP` step.

### `php spark` command fails post-deploy
- Use the runtime PHP CLI binary explicitly: `php8.2-cli spark cache:clear`.
- The deployment is at `$HOME/htdocs/`, so `spark` must be invoked relative to that path. Deploy Now executes post-deploy commands from `$HOME/htdocs/`.

## When to escalate to Deploy Now support

Email `deploynow-support@ionos.com` if:
- The MariaDB is not reachable for > 30 min.
- The webspace shows files but the URL returns 502/504.
- Repeated workflow failures with no log output.
