# Walkthrough: Laravel 11 SaaS on Deploy Now

Project name: `acme-saas`. Stack: Laravel 11, Inertia + Vue, MariaDB, Vite assets.

## 1. Validation output

```
Framework: Laravel 11.x
Confidence: high
PHP requirement: ^8.2
Vite detected: yes (vite.config.js)
Compatible with Deploy Now: Yes (officially detected framework)
```

Checklist passed except: `APP_KEY` secret missing → instruct user to add it.

## 2. Setup

1. `php artisan key:generate --show` → copy the `base64:...` output.
2. GitHub repo Settings → Secrets and variables → Actions → New secret:
   - Name: `APP_KEY`
   - Value: `base64:....`
3. ionos.space → New PHP project → connect repo `acme/acme-saas`.
4. Wizard prefills Laravel; confirm:
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
5. Wizard generates `.github/workflows/acme-saas-build.yaml`.
6. Pull locally; add the three files from step 3 below.
7. Customize the workflow per `workflows/customize-generated-workflow.md`.
8. Commit & push.

## 3. Files added

### `.deploy-now/acme-saas/config.yaml`

```yaml
bootstrap:
  excludes:
    - tests
    - phpunit.xml
    - docker-compose.yml
    - README.md

recurring:
  excludes:
    - storage/app/public
    - storage/app/uploads
    - storage/framework/cache
    - storage/framework/sessions
    - storage/framework/views
    - storage/logs
    - bootstrap/cache

post-deployment-remote-commands:
  - php8.2-cli artisan storage:link
  - php8.2-cli artisan migrate --force --no-interaction
  - php8.2-cli artisan config:cache
  - php8.2-cli artisan route:cache
  - php8.2-cli artisan view:cache
  - php8.2-cli artisan event:cache
  - php8.2-cli artisan queue:restart

runtime:
  cron-jobs:
    - command: php8.2-cli $HOME/htdocs/artisan schedule:run
      schedule: '* * * * *'
    - command: php8.2-cli $HOME/htdocs/artisan queue:work --stop-when-empty --max-time=60
      schedule: '* * * * *'
```

### `.deploy-now/acme-saas/.env.template`
(Standard template from `templates/env.template` — `APP_KEY=${{ secrets.APP_KEY }}` line resolves at deploy time.)

### `.deploy-now/acme-saas/public/.htaccess.template`
(Standard public `.htaccess` from `templates/public-htaccess.template`.)

## 4. First deploy result

```
✔ Build (1m 47s)
✔ Render templates
✔ Deploy to IONOS (54s)
✔ Post-deploy remote commands (12s)
```

Preview URL `https://acme-saas.ionos.space/` returned 200 with the Inertia welcome page.

## 5. Custom domain

Attached `app.acme.com` in dashboard. SSL provisioned in 90 seconds. `$IONOS_APP_URL` automatically updated; Inertia/Vite asset URLs picked up the new domain on the next push (config:cache rebuilt).
