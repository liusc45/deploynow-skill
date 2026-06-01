# Changelog

All notable changes to `deploynow-skill` are documented here. Format: [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [0.2.1] - 2026-06-01

### Fixed
- YAML parse error in `deploy-now-phalcon` description (the phrase "IMPORTANT: Phalcon requires..." was being read as a YAML mapping). Replaced with "Warning — Phalcon requires...". This unblocked `npx skills add --skill deploy-now-phalcon` and bumped discovery count from 11 to 12 skills.
- Renamed `skills/deploy-now-codeigniter4/templates/workflow-customization.yaml` to `.md` because the file is a commented snippet reference (not a valid standalone YAML config). Snyk IaC parser rejected it with SNYK-CLI-0012. Updated SKILL.md and customize-generated-workflow.md references.

### Security
- Ran Snyk IaC v1.1305.0 + Trivy MCP (misconfig + secret scans) on the catalog. Snyk IaC does not recognize Deploy Now's `config.yaml` schema (no rules for IONOS Deploy Now), so it returned 0 valid IaC files. Trivy reported 0 misconfigurations and 0 secrets. Manual audit found 0 hardcoded credentials, 0 `chmod 777`, 0 risky post-deploy commands, and 2 `http://` references (both in documentation explaining why to use HTTPS).
- CI4's `.htaccess` is the only template with full hardening (dotfile blocking, `-Indexes`, `X-Powered-By` unset). Other frameworks' `.htaccess` templates only handle URL rewriting.

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
