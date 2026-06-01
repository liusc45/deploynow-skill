# Validate Symfony project structure

| # | Check | Method |
|---|---|---|
| 1 | `bin/console` at repo root | `Glob: bin/console` |
| 2 | `composer.json` requires `symfony/framework-bundle` | `Grep` `composer.json` |
| 3 | `config/bundles.php` exists | `Glob: config/bundles.php` |
| 4 | Symfony version detection | Read `composer.json` → `require."symfony/framework-bundle"`. Map `^6.x` / `^7.x` |
| 5 | PHP requirement compatible | Symfony 6: ≥8.1. Symfony 7: ≥8.2 |
| 6 | `.gitignore` excludes `vendor/`, `.env`, `var/`, `node_modules/`, `public/build/`, `public/bundles/` | Read `.gitignore` |
| 7 | `public/index.php` exists | `Glob: public/index.php` |
| 8 | `config/` directory with YAML or PHP configs | `Glob: config/*` |
| 9 | If Webpack Encore present: `webpack.config.js` exists | `Glob: webpack.config.js` |
| 10 | `symfony.lock` exists (Flex-managed) | `Glob: symfony.lock` |

## Output

Emit table with pass/warn/fail; recommend exact fixes.

## If detection fails

Hand off to `deploy-now-codeigniter4` or `deploy-now-laravel` if those frameworks are detected instead.
