# deploynow-skill

Agent skills for deploying PHP applications to **IONOS Deploy Now** — 12 frameworks supported.

Works with [`skills`](https://www.npmjs.com/package/skills), the open agent skills CLI. Compatible with OpenCode, Claude Code, Cursor, Codex, Cline, and 50+ agents.

---

## Install

```bash
# List all skills
npx skills add liusc45/deploynow-skill --list

# Install a single skill
npx skills add liusc45/deploynow-skill --skill deploy-now-codeigniter4

# Install all
npx skills add liusc45/deploynow-skill --all
```

---

## Catalog

| Skill | Framework | Officially supported by Deploy Now? |
|---|---|---|
| `deploy-now-laravel` | Laravel 10 / 11 / 12 | **Yes** — auto-detected |
| `deploy-now-symfony` | Symfony 6 / 7 | **Yes** — auto-detected |
| `deploy-now-codeigniter4` | CodeIgniter 4 | No — manual PHP project |
| `deploy-now-cakephp` | CakePHP 4 / 5 | No — manual PHP project |
| `deploy-now-yii` | Yii2 | No — manual PHP project |
| `deploy-now-slim` | Slim 4 | No — manual PHP project |
| `deploy-now-phalcon` | Phalcon 5 | No — **⚠️ requires phalcon C extension** |
| `deploy-now-laminas` | Laminas MVC / Mezzio | No — manual PHP project |
| `deploy-now-spiral` | Spiral | **❌ INCOMPATIBLE** — requires RoadRunner |
| `deploy-now-fatfree` | Fat-Free Framework (F3) | No — manual PHP project |
| `deploy-now-flight` | Flight PHP | No — manual PHP project |
| `deploy-now-mezzio` | Mezzio (Laminas) | No — manual PHP project |

---

## What each skill does

1. Detect the framework in the user's repository.
2. Validate project structure for Deploy Now compatibility.
3. Walk through connecting the repo at [ionos.space](https://ionos.space).
4. Customize the GitHub Actions workflow Deploy Now generates.
5. Author runtime config files under `.deploy-now/<project>/`:
   - `config.yaml` — excludes, pre/post-deploy commands, cron jobs
   - `.env.template` — `$IONOS_DB_*`, `$IONOS_MAIL_*`, `$IONOS_APP_URL` placeholders
   - `.htaccess.template` — Apache rewrites and security headers
6. Diagnose common errors.

---

## Source of truth

- [docs.ionos.space](https://docs.ionos.space/)
- [docs.ionos.space/docs/deploy-php-apps/](https://docs.ionos.space/docs/deploy-php-apps/)
- [docs.ionos.space/docs/runtime-configuration/](https://docs.ionos.space/docs/runtime-configuration/)
- [docs.ionos.space/docs/deployment-configuration/](https://docs.ionos.space/docs/deployment-configuration/)
- [github.com/ionos-deploy-now/laravel-starter](https://github.com/ionos-deploy-now/laravel-starter)
- [github.com/ionos-deploy-now/symfony-starter](https://github.com/ionos-deploy-now/symfony-starter)

> **Important.** Deploy Now generates the `.github/workflows/<project>-build.yaml` automatically when you connect your repository. Users do not write it from scratch — they customize the generated file.

---

## Workflow version

Templates target **workflow v2** (current default). Workflow v1 differences are documented in the CodeIgniter4 skill's `references/workflow-v1-vs-v2.md`.

---

## Compatibility

Auto-installs to the correct path for whichever agent the `skills` CLI detects:

| Agent | Path |
|---|---|
| OpenCode | `.agents/skills/` or `~/.config/opencode/skills/` |
| Claude Code | `.claude/skills/` or `~/.claude/skills/` |
| Cursor | `.agents/skills/` or `~/.cursor/skills/` |
| Codex | `.agents/skills/` or `~/.codex/skills/` |
| Cline | `.agents/skills/` or `~/.agents/skills/` |

See the full agent list in the [`skills` CLI README](https://www.npmjs.com/package/skills#supported-agents).

---

## Contributing

See [`docs/CONTRIBUTING.md`](docs/CONTRIBUTING.md).

## License

[MIT](LICENSE) © liusc45
