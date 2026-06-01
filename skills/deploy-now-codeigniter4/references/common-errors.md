# Common errors and fixes

## Build phase (GitHub Actions)

| Error | Cause | Fix |
|---|---|---|
| `Your lock file does not contain a compatible set of packages` | `composer.lock` out of sync | Locally run `composer update --lock` and commit |
| `Your requirements could not be resolved to an installable set of packages` | PHP version mismatch | Match `php-version` in `Setup PHP` step to `composer.json` `require.php` |
| `php extension X not found` | Missing extension | Add it under `extensions:` in the `Setup PHP` step |
| `npm ci` fails with `EUSAGE` | `package-lock.json` mismatch | Locally run `npm install`, commit `package-lock.json` |
| Build green but no deploy step | Workflow file overwritten with v1-style steps | Re-run setup wizard in Deploy Now to regenerate |

## Deploy phase (Deploy Now action)

| Error | Cause | Fix |
|---|---|---|
| Action exits non-zero with `service-host` empty | Required input not injected | The workflow file was modified incorrectly. Restore the trailing deploy steps untouched. |
| `Permission denied (publickey)` | SSH key rotated | Trigger workflow re-run; Deploy Now re-injects |
| Deploy succeeds, files missing | Path excluded by `bootstrap.excludes` | Remove the path from `excludes:` and redeploy |

## Runtime (browser-visible)

| Symptom | Cause | Fix |
|---|---|---|
| `403 Forbidden` on the root URL | Publish directory not set to `public/` in the dashboard | Open Deploy Now dashboard → project settings → set publish dir to `public` → redeploy |
| `404 Not Found` on every route except `/` | `.htaccess` missing or `mod_rewrite` rules wrong | Verify both `.htaccess.template` files exist and contain the rewrite block |
| `500 Internal Server Error` | PHP fatal — open `writable/logs/log-YYYY-MM-DD.php` via deployment viewer | Address the logged error |
| `Unable to connect to your database server using the provided settings` | Wrong placeholder syntax in `.env.template` | v2: `$IONOS_DB_HOST`. v1: `{{ .runtime.db.host }}` |
| `An uncaught Exception was encountered: Type: ErrorException — touch(): Unable to create file` | `writable/` not writable or path missing | Confirm `writable/cache/`, `writable/logs/`, `writable/session/`, `writable/uploads/` exist with `.gitkeep` |
| `The encryption key in your .env file is either invalid or missing` | `encryption.key` empty | Generate locally with `php spark key:generate --show`, store as GitHub secret, reference in `.env.template` |
| `Mixed Content` warnings | `app.baseURL` starts with `http://` | Set `app.forceGlobalSecureRequests = true` and use `$IONOS_APP_URL` (already HTTPS) |
| Uploads disappear after every deploy | `public/uploads` not under `recurring.excludes` | Add to `.deploy-now/<project>/config.yaml` |
| Sessions reset on every deploy | `writable/session` not under `recurring.excludes` | Add to `recurring.excludes` |
| Cron job not running | Cron syntax wrong or PHP binary not absolute | Use `php8.2-cli $HOME/htdocs/spark <task>` and verify schedule with crontab.guru |
| `Class "App\Controllers\X" not found` | Composer autoload not optimized | Confirm `composer install --optimize-autoloader` ran |

## Deploy Now-specific gotchas

| Symptom | Note |
|---|---|
| Cannot SSH into the server | Deploy Now does not offer SSH access. Manage files via the deployment viewer in the dashboard. |
| Database backup needed | Backups are taken daily and retained 7 days. Restore via Deploy Now support: `deploynow-support@ionos.com`. |
| Need a queue worker | No long-running processes. Use a cron job invoking `php spark tasks:run` every minute. |
| Need Redis | Not provided by Deploy Now. Use an external managed Redis or fall back to file cache. |
| Workflow file regenerated and lost edits | Customizations are kept by Deploy Now in the wizard, but a manual re-run can overwrite. Always commit edits and rebase if necessary. |
