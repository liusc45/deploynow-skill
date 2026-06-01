# Post-deploy recipes for Symfony

Run as `post-deployment-remote-commands` in `.deploy-now/<project>/config.yaml`. Working directory: `$HOME/htdocs/`. Use explicit PHP CLI binary: `php8.2-cli`.

## Required for any deploy

```yaml
post-deployment-remote-commands:
  - php8.2-cli bin/console cache:clear --env=prod
  - php8.2-cli bin/console doctrine:migrations:migrate --no-interaction
  - php8.2-cli bin/console cache:warmup --env=prod
```

## With AssetMapper (Symfony 6.4+ / 7+)

```yaml
  - php8.2-cli bin/console asset-map:compile
```

## With Webpack Encore

Assets are built during the GitHub Actions build step (`npm ci && npm run build`). No runtime command needed. If bundles publish assets:

```yaml
  - php8.2-cli bin/console assets:install public
```

## Doctrine migrations safety

`doctrine:migrations:migrate --no-interaction` applies all pending migrations. For production:
- Keep migrations backward-compatible.
- Use `--dry-run` to preview SQL before committing.
- For breaking schema changes, use a manual workflow_dispatch trigger instead of automatic post-deploy.

## Messenger queues

If using Symfony Messenger with the Doctrine transport:

```yaml
  - php8.2-cli bin/console messenger:setup-transports
```

Plus a cron job for async workers:

```yaml
runtime:
  cron-jobs:
    - command: php8.2-cli $HOME/htdocs/bin/console messenger:consume async --time-limit=60 --memory-limit=128M
      schedule: '* * * * *'
```

## Cron jobs (scheduled tasks)

Symfony's built-in scheduler (6.2+) requires a cron entry:

```yaml
runtime:
  cron-jobs:
    - command: php8.2-cli $HOME/htdocs/bin/console messenger:consume scheduler_default --time-limit=60
      schedule: '* * * * *'
```

## Warmup without clearing

If cache:clear fails for some reason, try warmup only:

```yaml
  - php8.2-cli bin/console cache:warmup --env=prod
```

## Clearing individual caches

```yaml
  - php8.2-cli bin/console cache:pool:clear cache.app
  - php8.2-cli bin/console cache:pool:clear cache.system
```

## Routes and config debug (post-deploy verification)

```yaml
  - php8.2-cli bin/console debug:router --env=prod | head -20
  - php8.2-cli bin/console about --env=prod
```

Useful to add temporarily, then remove after verifying.
