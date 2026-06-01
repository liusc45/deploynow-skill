# Walkthrough: deploying a real CodeIgniter 4 app

Scenario: an agent is asked to ship a CI4 4.5 application that uses MariaDB, the Shield auth package, and image uploads stored under `public/uploads/`. Project name in Deploy Now: `ci4-blog`.

## 1. Detection output

```
Framework: CodeIgniter 4
Confidence: high
Strong signals: spark, system/CodeIgniter.php, composer require codeigniter4/framework
PHP requirement (composer.json): ^8.1
Compatible with Deploy Now: Yes (manual PHP project — reasonable inference)
```

## 2. Validation checklist

```
| # | Check                                                | Status |
|---|------------------------------------------------------|--------|
| 1 | public/index.php exists                              | ✅     |
| 2 | composer.json requires codeigniter4/framework        | ✅     |
| 3 | PHP ≥ 8.1                                            | ✅     |
| 4 | .gitignore excludes writable/cache, vendor, .env     | ⚠️ missing writable/debugbar/* |
| 5 | app.baseURL env-driven                               | ✅     |
| 6 | writable/ subdirs present with .gitkeep              | ✅     |
| 7 | .deploy-now/ci4-blog/config.yaml                     | ❌ to be generated |
| 8 | .deploy-now/ci4-blog/.env.template                   | ❌ to be generated |
| 9 | .deploy-now/ci4-blog/.htaccess.template              | ❌ to be generated |
| 10| Publish dir = public/ in dashboard                   | (manual) |
```

Auto-fix applied: appended `writable/debugbar/*` to `.gitignore`.

## 3. Generated files

### `.deploy-now/ci4-blog/config.yaml`

```yaml
bootstrap:
  excludes:
    - tests
    - .editorconfig
    - phpunit.xml.dist
    - README.md

recurring:
  excludes:
    - public/uploads          # user-uploaded images
    - writable/uploads
    - writable/cache
    - writable/logs
    - writable/session
    - writable/debugbar

pre-deployment-remote-commands:
  - echo "Deploying ci4-blog"

post-deployment-remote-commands:
  - php8.2-cli spark cache:clear
  - php8.2-cli spark migrate --all     # idempotent for this project

runtime:
  cron-jobs:
    - command: php8.2-cli $HOME/htdocs/spark queue:work --once
      schedule: '*/5 * * * *'
```

### `.deploy-now/ci4-blog/.env.template`

```ini
CI_ENVIRONMENT = production

app.baseURL = '$IONOS_APP_URL'
app.indexPage = ''
app.forceGlobalSecureRequests = true

database.default.hostname = $IONOS_DB_HOST
database.default.database = $IONOS_DB_NAME
database.default.username = $IONOS_DB_USERNAME
database.default.password = $IONOS_DB_PASSWORD
database.default.DBDriver = MySQLi
database.default.port = 3306
database.default.charset = utf8mb4
database.default.DBCollat = utf8mb4_general_ci

encryption.key = hex2bin:${ENCRYPTION_KEY}

session.driver = 'CodeIgniter\Session\Handlers\FileHandler'
logger.threshold = 4

email.protocol = 'smtp'
email.SMTPHost = $IONOS_MAIL_HOST
email.SMTPPort = $IONOS_MAIL_PORT
email.SMTPUser = $IONOS_MAIL_USERNAME
email.SMTPPass = $IONOS_MAIL_PASSWORD
email.SMTPCrypto = $IONOS_MAIL_ENCRYPTION
email.fromEmail = $IONOS_MAIL_FROM_ADDRESS
email.fromName = 'CI4 Blog'

# Shield-specific
auth.session_driver = 'CodeIgniter\Shield\Authentication\Authenticators\Session'
```

### `.deploy-now/ci4-blog/.htaccess.template`

(Standard root `.htaccess` from the bundled template — redirects everything to `public/index.php`.)

### `.deploy-now/ci4-blog/public/.htaccess.template`

(Standard public `.htaccess` from the bundled template — adds security headers.)

## 4. Deployment steps

1. Sign in at [ionos.space](https://ionos.space).
2. Click **New project** → **PHP project**.
3. Select the GitHub repo `acme/ci4-blog`.
4. Wizard step: build dependencies → check **Composer** and **Node.js** (if Vite is in use). Else uncheck Node.
5. Wizard step: build commands → replace with:
   ```
   composer install --no-dev --optimize-autoloader --no-interaction --prefer-dist
   ```
6. Wizard step: publish directory → **`public`**.
7. Wizard step: runtime → PHP 8.2; provision MariaDB; provision send-mail account.
8. Add the GitHub secret `ENCRYPTION_KEY` (output of `php spark key:generate --show`) under repo Settings → Secrets.
9. Finish wizard. Deploy Now commits `.github/workflows/ci4-blog-build.yaml` to the repo.
10. Pull, then add the four files from step 3 to `.deploy-now/ci4-blog/`.
11. Customize `.github/workflows/ci4-blog-build.yaml` per `workflows/customize-generated-workflow.md` (PHP 8.2 + extensions, Composer flags).
12. Commit and push.
13. Watch the Actions tab. The workflow should be green within ~3 minutes.

## 5. Post-deploy verification

```bash
# Replace with your preview URL or custom domain:
SITE=https://ci4-blog.ionos.space

curl -sI $SITE/                              # → 200, with HSTS header
curl -sI $SITE/login                         # → 200 (Shield route)
curl -s  $SITE/health-check                  # → "OK" if you wired one
curl -sI $SITE/uploads/test.jpg              # → 200 if a real file exists
```

In the Deploy Now dashboard:
- **Logs** → no `[error]` entries.
- **Database** (phpMyAdmin) → migrations table populated.
- **Visitor statistics** → first hits show up within ~10 minutes.

## 6. Three real problems that occurred and how they were fixed

| Problem | Resolution |
|---|---|
| `403 Forbidden` on first visit | Publish dir was left at `./` instead of `public`. Updated in dashboard, redeployed. |
| `Unable to connect to your database server` | `.env.template` had `database.default.password = $IONOS_DB_PASS` (wrong). Renamed to `$IONOS_DB_PASSWORD`. |
| Uploaded images vanished after second deploy | `public/uploads` was missing from `recurring.excludes`. Added. |

## 7. Custom domain

Attached `ci4-blog.example.com` in dashboard. Le's Encrypt cert provisioned in 2 minutes. `$IONOS_APP_URL` automatically updated; no template changes needed.
