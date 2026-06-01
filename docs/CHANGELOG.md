# Changelog

All notable changes to `deploynow-skill` are documented here. Format: [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [0.2.0] - 2026-06-01

### Added
- 9 new framework skills:
  - `deploy-now-cakephp` — CakePHP 4/5 (webroot/ publish dir)
  - `deploy-now-yii` — Yii2 (web/ publish dir)
  - `deploy-now-slim` — Slim 4 (public/ publish dir)
  - `deploy-now-phalcon` — Phalcon 5 (public/ publish dir, requires C extension)
  - `deploy-now-laminas` — Laminas MVC / Mezzio (public/ publish dir)
  - `deploy-now-spiral` — Spiral (INCOMPATIBLE — documents RoadRunner requirement)
  - `deploy-now-fatfree` — Fat-Free Framework F3 (root publish dir)
  - `deploy-now-flight` — Flight PHP (public/ publish dir)
  - `deploy-now-mezzio` — Mezzio PSR-15 middleware (public/ publish dir)

## [0.1.0] - 2026-06-01

### Added
- Initial catalog with three skills:
  - `deploy-now-codeigniter4` — manual PHP project configuration for CI4 (workflow v2)
  - `deploy-now-laravel` — official auto-detected framework (workflow v2)
  - `deploy-now-symfony` — official auto-detected framework (workflow v2)
- Templates for `.deploy-now/<project>/config.yaml`, `.env.template`, `.htaccess.template`.
- References sourced from `docs.ionos.space` and the official starter repos.
- Realistic walkthrough example for each skill.
