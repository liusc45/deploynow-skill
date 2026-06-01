# Validate Laravel project structure

| # | Check | Method |
|---|---|---|
| 1 | `artisan` at repo root | `Glob: artisan` |
| 2 | `composer.json` requires `laravel/framework` | `Grep` `composer.json` for `"laravel/framework"` |
| 3 | `bootstrap/app.php` exists | `Glob: bootstrap/app.php` |
| 4 | Laravel version detection | Read `composer.json` → `require."laravel/framework"`. Map `^10.x` / `^11.x` / `^12.x` |
| 5 | PHP requirement compatible | `composer.json` `require.php`: Laravel 10 → ≥8.1; Laravel 11/12 → ≥8.2 |
| 6 | `.gitignore` excludes `vendor/`, `.env`, `node_modules/`, `public/build`, `public/hot`, `storage/*.key`, `storage/pail` | Read `.gitignore` |
| 7 | `public/index.php` exists | `Glob: public/index.php` |
| 8 | `database/migrations/` exists | `Glob: database/migrations/*.php` |
| 9 | If Vite present: `vite.config.js` exists | `Glob: vite.config.js` |
| 10 | If Sail used: `docker-compose.yml` excluded from deployment | List under `bootstrap.excludes` |

## Output

Emit table with pass/warn/fail; recommend exact fixes.

## If detection fails

Hand off to `deploy-now-codeigniter4` (CI4) or `deploy-now-symfony` (Symfony) if those frameworks are detected instead.
