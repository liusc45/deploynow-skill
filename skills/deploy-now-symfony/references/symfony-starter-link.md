# Official Symfony starter

IONOS maintains an official Symfony starter exercising the full Deploy Now stack:

**Repo:** https://github.com/ionos-deploy-now/symfony-starter

It covers:
- `.deploy-now/` configuration
- Webpack Encore + Tailwind CSS build
- `.env` adapted for production deploy
- `.htaccess` for `public/`

## How to use it

### Option A: Deploy directly
Click the **Deploy to IONOS** button on the starter README.

### Option B: Reference snippets
Clone, inspect:
- `.deploy-now/config.yaml`
- `.deploy-now/.env.template`
- `.deploy-now/.htaccess.template`
- `.deploy-now/public/.htaccess.template`

> Starter uses workflow v1 placeholders (`{{ .runtime.db.host }}`). This skill targets v2 (`$IONOS_DB_HOST`). Adjust accordingly.

## Helpful links

- [PHP Apps on Deploy Now](https://docs.ionos.space/docs/deploy-php-apps/)
- [Build configuration](https://docs.ionos.space/docs/github-actions-customization/)
- [Deployment configuration](https://docs.ionos.space/docs/deployment-configuration/)
- [Runtime configuration](https://docs.ionos.space/docs/runtime-configuration/)
