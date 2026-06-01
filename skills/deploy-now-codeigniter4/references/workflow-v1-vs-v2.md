# Workflow v1 vs v2 — quick reference

Source: https://docs.ionos.space/docs/git-integration/#workflow-versions

## Identifying which version you are on

Check your generated workflow file path:

| Path you see | Version |
|---|---|
| `.github/workflows/deploy-now.yaml` | **v1** |
| `.github/workflows/<project>-build.yaml` | **v2** |

Or check the runtime config path:

| Path you see | Version |
|---|---|
| `.deploy-now/config.yaml` | **v1** |
| `.deploy-now/<project>/config.yaml` | **v2** |

## Side-by-side differences

| Aspect | Workflow v1 | Workflow v2 |
|---|---|---|
| Workflow file | `.github/workflows/deploy-now.yaml` | `.github/workflows/<project>-build.yaml` |
| Runtime config | `.deploy-now/config.yaml` | `.deploy-now/<project>/config.yaml` |
| `.env.template` | `.deploy-now/.env.template` | `.deploy-now/<project>/.env.template` |
| Conditional run guards | `if: ${{ steps.project.outputs.deployment-enabled == 'true' }}` on every step | Not needed — guards are inside reusable steps |
| Placeholder syntax | `{{ .runtime.db.host }}` (Go template) | `$IONOS_DB_HOST` (shell variable) |
| Custom secrets | Must be re-listed under `template-renderer-action` `secrets:` input | Automatically passed |
| `dist-folder` | Set inside the deploy step | Replaced by top-level `env: DEPLOYMENT_FOLDER:` |

## Migrating v1 → v2

Deploy Now performs the migration. End users do not migrate manually — the platform updates the workflow when you re-run the project setup. Old projects still on v1 keep working.

## Which version this skill targets

**v2.** All templates and references in this skill use the v2 paths and placeholder syntax. If a project is on v1, the agent must:

1. Move runtime files from `.deploy-now/` to `.deploy-now/<project>/`.
2. Convert `$IONOS_DB_HOST` placeholders back to `{{ .runtime.db.host }}`.
3. Wrap any custom secrets in the `template-renderer-action` step.
