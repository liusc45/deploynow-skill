# Phalcon on Deploy Now — notes

> **Status: reasonable inference.** Phalcon is not officially supported by Deploy Now.
> **⚠️ CRITICAL: Phalcon requires the `phalcon` PHP C extension.** This is a compiled `.so`/`.dll` file that must be installed on the server. Shared hosting rarely supports it. **Verify with Deploy Now support before investing time.**

## Document root

Phalcon serves from `public/`. Root `.htaccess` redirects to `public/`.

## Phalcon-specific rewrite rule

Unlike other frameworks, Phalcon uses `?_url=/$1`:

```apache
RewriteRule ^((?s).*)$ index.php?_url=/$1 [QSA,L]
```

## Directory structure

```
├── app/
│   ├── config/
│   ├── controllers/
│   ├── models/
│   └── views/
├── public/           # ← publish directory
│   ├── .htaccess
│   └── index.php
├── vendor/
├── composer.json
└── .env
```

## Extension verification

In the GitHub Actions workflow, add:

```yaml
- name: Verify Phalcon extension
  run: php -m | grep phalcon || echo "WARNING: phalcon extension not found"
```

## Source

- https://docs.phalcon.io/latest/webserver-setup/
