# Official Laravel starter

The IONOS team maintains an official Laravel starter that exercises the full Deploy Now stack:

**Repo:** https://github.com/ionos-deploy-now/laravel-starter

It is the canonical reference for:
- `.deploy-now/` configuration (note: starter is on workflow v1; placeholder syntax differs from this skill which targets v2)
- Build steps, Vite + Tailwind setup
- `.env.example` adapted for production deploy
- `.htaccess` for the root and `public/`

## How to use it

### Option A: Deploy directly (zero code)
Click the **Deploy to IONOS** button on the starter README. It walks through repo creation and Deploy Now setup in one flow.

### Option B: Reference snippets
Clone the starter, inspect:
- `.deploy-now/config.yaml`
- `.deploy-now/.env.template`
- `.deploy-now/.htaccess.template`
- `.deploy-now/public/.htaccess.template`

Adapt the syntax: starter uses workflow v1 (`{{ .runtime.db.host }}`), this skill targets v2 (`$IONOS_DB_HOST`).

## Helpful links from the starter README

- [Deploy Now docs](https://docs.ionos.space/)
- [PHP Apps on Deploy Now](https://docs.ionos.space/docs/deploy-php-apps/)
- [Build configuration](https://docs.ionos.space/docs/github-actions-customization/)
- [Deployment configuration](https://docs.ionos.space/docs/deployment-configuration/)
- [Runtime configuration](https://docs.ionos.space/docs/runtime-configuration/)
