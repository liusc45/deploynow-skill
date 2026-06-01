# Custom domain and TLS

Source: https://docs.ionos.space/docs/domain-tls/

## Default URL

Each Deploy Now project gets a free preview URL: `https://<random>.ionos.space`. SSL is included.

## Attaching a custom domain

1. Open the project in the Deploy Now dashboard.
2. Go to **Domains** → **Connect domain**.
3. Enter the FQDN (e.g. `example.com` or `app.example.com`).
4. If the domain is registered with IONOS: connection is automatic.
5. If the domain is registered elsewhere: switch the nameservers to IONOS, OR add the DNS records Deploy Now shows in the dashboard.

Deploy Now provisions a Let's Encrypt certificate automatically once DNS resolves. Renewal is automatic.

## After attaching the domain

Update `.env.template`:

```
app.baseURL = '$IONOS_APP_URL'
```

`$IONOS_APP_URL` is automatically set to your custom domain by Deploy Now once DNS points to the webspace. No manual update is needed.

## www / apex redirect

Configure in `public/.htaccess.template`. See `references/htaccess-recipes.md` → "Strip www." or "Add www.".

## Multiple domains

Attach as many domains as you like in the dashboard. They all serve the same deployment unless you use **Multi Deployments** (separate IONOS branches per environment, see [docs.ionos.space/docs/multi-deployments/](https://docs.ionos.space/docs/multi-deployments/)).

## Staging deployments

[Staging deployments](https://docs.ionos.space/docs/staging-deployments/) let additional Git branches deploy automatically to separate URLs (typically `<project>-<branch>.ionos.space`). Use the Deploy Now dashboard to enable a branch as a staging deployment. The same `.deploy-now/<project>/` config applies.
