# Walkthrough: Symfony 7 e-commerce on Deploy Now

Project name: `acme-shop`. Stack: Symfony 7, Doctrine ORM, MariaDB, Webpack Encore + Tailwind CSS.

## 1. Validation output

```
Framework: Symfony 7.x
Confidence: high
PHP requirement: ^8.2
Webpack Encore detected: yes (webpack.config.js)
Compatible with Deploy Now: Yes (officially detected framework)
```

Checklist passed except: `APP_SECRET` secret missing → instruct user to add it.

## 2. Setup

1. `php -r "echo bin2hex(random_bytes(16));"` → copy hex string.
2. GitHub repo Settings → Secrets and variables → Actions → New secret:
   - Name: `APP_SECRET`
   - Value: the hex string.
3. ionos.space → New PHP project → connect repo `acme/acme-shop`.
4. Wizard prefills Symfony; confirm:
   - Publish dir: `public`
   - Build dependencies: Composer + Node 20
   - Build commands:
     ```
     composer install --no-dev --optimize-autoloader --no-interaction
     npm ci
     npm run build
     ```
   - PHP runtime: 8.2
   - Provision MariaDB and send-mail account.
5. Wizard generates `.github/workflows/acme-shop-build.yaml`.
6. Pull locally; add the three runtime files below.
7. Customize the workflow per `workflows/customize-generated-workflow.md`.
8. Commit & push.

## 3. Files added

### `.deploy-now/acme-shop/config.yaml`

```yaml
bootstrap:
  excludes:
    - tests
    - phpunit.xml.dist
    - .editorconfig
    - docker-compose.yml
    - README.md

recurring:
  excludes:
    - var/cache
    - var/log
    - var/sessions
    - var/data
    - public/uploads
    - public/bundles

post-deployment-remote-commands:
  - php8.2-cli bin/console cache:clear --env=prod
  - php8.2-cli bin/console doctrine:migrations:migrate --no-interaction
  - php8.2-cli bin/console cache:warmup --env=prod
  - php8.2-cli bin/console assets:install public

runtime:
  cron-jobs: []
```

### `.deploy-now/acme-shop/.env.template`

```ini
APP_ENV=prod
APP_DEBUG=0
APP_SECRET=${{ secrets.APP_SECRET }}
APP_URL=$IONOS_APP_URL

DATABASE_URL="mysql://$IONOS_DB_USERNAME:$IONOS_DB_PASSWORD@$IONOS_DB_HOST:3306/$IONOS_DB_NAME?serverVersion=mariadb-10.6&charset=utf8mb4"

MAILER_DSN="smtp://$IONOS_MAIL_USERNAME:$IONOS_MAIL_PASSWORD@$IONOS_MAIL_HOST:$IONOS_MAIL_PORT"
```

### `.deploy-now/acme-shop/public/.htaccess.template`

(Standard Symfony public/.htaccess from `templates/public-htaccess.template`.)

## 4. First deploy result

```
✔ Build (2m 12s)
✔ Render templates
✔ Deploy to IONOS (1m 02s)
✔ Post-deploy remote commands (18s)
```

Preview URL `https://acme-shop.ionos.space/` returned 200 with the Symfony welcome page.

## 5. Custom domain

Attached `shop.acme.com` in dashboard. SSL provisioned in ~90 seconds. `$IONOS_APP_URL` automatically updated. Routes and asset URLs picked up the new domain via `config:cache`.

## 6. Troubleshooting that occurred

| Problem | Resolution |
|---|---|
| `500` on first load | `var/` was excluded from deploy but not listed under `recurring.excludes` so the first deploy wiped it. Added `var/cache`, `var/log`, `var/sessions` to `recurring.excludes`. |
| `DatabaseException: An exception occurred in driver: could not find driver` | `pdo_mysql` extension missing in `Setup PHP` step. Added `mysqli, pdo_mysql` to `extensions:`. |
| Asset 404s for CSS/JS | `public/build/` was in `recurring.excludes` but also rebuilt every deploy. Removed from excludes since the build step always regenerates it. |
