# deploynow-skill

Agent skills for deploying PHP applications to **IONOS Deploy Now** — covering **CodeIgniter 4**, **Laravel**, and **Symfony**.

These skills work with [`skills`](https://www.npmjs.com/package/skills), the open agent skills CLI. Compatible with OpenCode, Claude Code, Cursor, Codex, Cline, and 50+ more agents.

---

## Install

```bash
# List all skills in this catalog
npx skills add liusc45/deploynow-skill --list

# Install a single skill
npx skills add liusc45/deploynow-skill --skill deploy-now-codeigniter4

# Install all three
npx skills add liusc45/deploynow-skill --all

# Install globally (available across all projects)
npx skills add liusc45/deploynow-skill --all -g
```

---

## Catalog

| Skill | Framework | Officially supported by Deploy Now? |
|---|---|---|
| `deploy-now-codeigniter4` | CodeIgniter 4 | No — configured as a generic PHP project (manual setup, reasonable inference) |
| `deploy-now-laravel` | Laravel 10 / 11 / 12 | **Yes** — auto-detected |
| `deploy-now-symfony` | Symfony 6 / 7 | **Yes** — auto-detected |

---

## What each skill does

Each skill helps an AI coding agent:

1. Detect the framework in the user's repository.
2. Validate the project structure for Deploy Now compatibility.
3. Walk the user through connecting the repo at [ionos.space](https://ionos.space).
4. Customize the GitHub Actions workflow that **Deploy Now generates automatically**.
5. Author the runtime configuration files under `.deploy-now/<project>/`:
   - `config.yaml` — file persistency, excludes, pre/post-deploy remote commands, cron jobs
   - `.env.template` — application config with `$IONOS_DB_*`, `$IONOS_MAIL_*`, `$IONOS_APP_URL` placeholders
   - `.htaccess.template` — Apache rewrites and security headers
6. Diagnose common errors (403, missing `index.php`, MariaDB connection, `.htaccess` issues, `writable/` permissions for CI4).

---

## Source of truth

All guidance is grounded in the official IONOS Deploy Now documentation and starter repos:

- [docs.ionos.space](https://docs.ionos.space/) — primary docs
- [docs.ionos.space/docs/deploy-php-apps/](https://docs.ionos.space/docs/deploy-php-apps/)
- [docs.ionos.space/docs/runtime-configuration/](https://docs.ionos.space/docs/runtime-configuration/)
- [docs.ionos.space/docs/deployment-configuration/](https://docs.ionos.space/docs/deployment-configuration/)
- [docs.ionos.space/docs/github-actions-customization/](https://docs.ionos.space/docs/github-actions-customization/)
- [github.com/ionos-deploy-now/laravel-starter](https://github.com/ionos-deploy-now/laravel-starter)
- [github.com/ionos-deploy-now/symfony-starter](https://github.com/ionos-deploy-now/symfony-starter)

> **Important:** Deploy Now generates the `.github/workflows/<project>-build.yaml` automatically when you connect your repository. Users do not write it from scratch — they customize the generated file. These skills reflect that reality.

---

## Workflow version

Templates target **workflow v2** (current default for projects created since 11/2022). Workflow v1 differences are documented in `skills/deploy-now-codeigniter4/references/workflow-v1-vs-v2.md`.

---

## Compatibility

Auto-installs to the correct path for whichever agent the `skills` CLI detects:

| Agent | Path |
|---|---|
| OpenCode | `.agents/skills/` (project) or `~/.config/opencode/skills/` (global) |
| Claude Code | `.claude/skills/` or `~/.claude/skills/` |
| Cursor | `.agents/skills/` or `~/.cursor/skills/` |
| Codex | `.agents/skills/` or `~/.codex/skills/` |
| Cline | `.agents/skills/` or `~/.agents/skills/` |

See the full agent list in the [`skills` CLI README](https://www.npmjs.com/package/skills#supported-agents).

---

## Contributing

See [`docs/CONTRIBUTING.md`](docs/CONTRIBUTING.md). Issues and PRs welcome.

## License

[MIT](LICENSE) © liusc45
