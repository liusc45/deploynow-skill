# Post-deploy recipes for Laravel

These run as `post-deployment-remote-commands` in `.deploy-now/<project>/config.yaml`. The runtime working directory is `$HOME/htdocs/`. Use the explicit PHP CLI binary `php8.2-cli` (or the version you chose).

## Required for any deploy

```yaml
post-deployment-remote-commands:
  - php8.2-cli artisan storage:link
  - php8.2-cli artisan migrate --force --no-interaction
  - php8.2-cli artisan config:cache
  - php8.2-cli artisan route:cache
  - php8.2-cli artisan view:cache
  - php8.2-cli artisan event:cache
```

## When using queues

If `QUEUE_CONNECTION=database`:

```yaml
  - php8.2-cli artisan queue:restart
```

Plus a cron job:

```yaml
runtime:
  cron-jobs:
    - command: php8.2-cli $HOME/htdocs/artisan queue:work --stop-when-empty --max-time=60
      schedule: '* * * * *'
```

## Laravel scheduler (always include)

```yaml
runtime:
  cron-jobs:
    - command: php8.2-cli $HOME/htdocs/artisan schedule:run
      schedule: '* * * * *'
```

## Filament / Nova / Telescope

For each, run after migrations:

```yaml
  - php8.2-cli artisan filament:upgrade
  # or
  - php8.2-cli artisan telescope:publish
```

## Horizon

Horizon needs a long-running process and is **not compatible** with Deploy Now's runtime model. Use the database queue + scheduler pattern instead.

## Cache warmup

```yaml
  - php8.2-cli artisan cache:clear
  - php8.2-cli artisan optimize
```

`optimize` is a meta-command that runs `config:cache`, `route:cache`, `view:cache`. If you call it, omit the individual cache commands.

## Migrations safety

`migrate --force` skips the production confirmation prompt. Make migrations idempotent and reversible. For destructive schema changes, run them via a manually-triggered workflow_dispatch instead of automatically.

## Database seeders (occasional)

```yaml
  # Run only when needed; remove afterwards or guard with idempotent code:
  - php8.2-cli artisan db:seed --force --class=ProductionSeeder
```
