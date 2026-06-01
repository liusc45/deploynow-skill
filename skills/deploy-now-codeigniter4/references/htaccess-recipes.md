# .htaccess recipes for CodeIgniter 4 on Deploy Now

Source: https://docs.ionos.space/docs/apache-configuration-htaccess/

## Where to put `.htaccess` files

Deploy Now renders `.htaccess.template` files from `.deploy-now/<project>/` into the runtime. The relative path inside `.deploy-now/<project>/` becomes the path on the runtime.

| Repo path | Runtime path |
|---|---|
| `.deploy-now/<project>/.htaccess.template` | `.htaccess` (in $HOME/htdocs/) |
| `.deploy-now/<project>/public/.htaccess.template` | `public/.htaccess` |

## Recipe: redirect everything to `public/`

Root `.htaccess`. Already in `templates/htaccess.template`.

```apache
<IfModule mod_rewrite.c>
    RewriteEngine On
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteRule ^(.*)$ public/index.php/$1 [L]
</IfModule>
```

## Recipe: force HTTPS

Add to the root `.htaccess` (Deploy Now also handles SSL termination, so this is mostly for visitors hitting `http://`).

```apache
<IfModule mod_rewrite.c>
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^ https://%{HTTP_HOST}%{REQUEST_URI} [L,R=301]
</IfModule>
```

## Recipe: strip `www.` (or add it)

Strip `www.`:

```apache
RewriteCond %{HTTP_HOST} ^www\.(.+)$ [NC]
RewriteRule ^ https://%1%{REQUEST_URI} [L,R=301]
```

Add `www.`:

```apache
RewriteCond %{HTTP_HOST} !^www\. [NC]
RewriteRule ^ https://www.%{HTTP_HOST}%{REQUEST_URI} [L,R=301]
```

## Recipe: long-lived caching for static assets

Add to `public/.htaccess`:

```apache
<IfModule mod_expires.c>
    ExpiresActive On
    ExpiresByType image/jpeg "access plus 1 year"
    ExpiresByType image/png "access plus 1 year"
    ExpiresByType image/webp "access plus 1 year"
    ExpiresByType image/svg+xml "access plus 1 year"
    ExpiresByType text/css "access plus 1 month"
    ExpiresByType application/javascript "access plus 1 month"
    ExpiresByType font/woff2 "access plus 1 year"
</IfModule>
```

## Recipe: gzip compression

```apache
<IfModule mod_deflate.c>
    AddOutputFilterByType DEFLATE text/html text/plain text/css application/json
    AddOutputFilterByType DEFLATE application/javascript text/javascript
    AddOutputFilterByType DEFLATE application/xml text/xml image/svg+xml
</IfModule>
```

## Recipe: security headers

```apache
<IfModule mod_headers.c>
    Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains"
    Header always set X-Frame-Options "SAMEORIGIN"
    Header always set X-Content-Type-Options "nosniff"
    Header always set Referrer-Policy "strict-origin-when-cross-origin"
    Header always set Permissions-Policy "geolocation=(), microphone=(), camera=()"
</IfModule>
```

## Recipe: block direct PHP execution outside `public/`

The redirect-everything-to-public recipe handles this implicitly. Belt and braces:

```apache
<FilesMatch "\.(env|log|ini|sqlite)$">
    Order allow,deny
    Deny from all
</FilesMatch>
```
