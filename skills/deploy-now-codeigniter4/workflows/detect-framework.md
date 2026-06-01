# Detect CodeIgniter 4

## Strong signals (≥ 2 → confirmed CI4)

Run these checks against the repository root.

| Signal | Check |
|---|---|
| `spark` CLI present | `Glob: spark` (no extension, executable) |
| Framework in composer | `Grep` `composer.json` for `"codeigniter4/framework"` |
| Core file present | `Glob: system/CodeIgniter.php` |
| App config present | `Glob: app/Config/App.php` |
| Writable tree present | `Glob: writable/{cache,logs,session,uploads}/` |

## Weak signals (additional evidence)

| Signal | Check |
|---|---|
| `public/index.php` references `FCPATH` or `system/bootstrap.php` | `Grep: FCPATH` in `public/index.php` |
| `env` file (no leading dot) | `Glob: env` at repo root |
| `phpunit.xml.dist` references `tests/_support` | `Grep: tests/_support` in `phpunit.xml.dist` |

## Confidence levels

| Confidence | Criteria |
|---|---|
| **high** | ≥ 2 strong signals |
| **medium** | 1 strong + ≥ 1 weak |
| **low** | only weak signals — flag as **reasonable inference** in the diagnosis output |

## If the project is Laravel or Symfony instead

Hand off to:
- `deploy-now-laravel` skill (if `artisan` exists and `composer.json` has `laravel/framework`)
- `deploy-now-symfony` skill (if `bin/console` exists and `composer.json` has `symfony/framework-bundle`)

## Output the agent should emit

```
Framework: CodeIgniter 4
Confidence: high   (signals: spark + system/CodeIgniter.php + composer require)
PHP requirement (composer.json require.php): ^8.1
Compatible with Deploy Now: Yes (manual PHP project — reasonable inference)
```
