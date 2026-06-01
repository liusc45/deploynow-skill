# Validate project structure

Run all 10 checks. Report results as a table.

## Checks

### 1. Public document root
- **Rule:** `public/index.php` must exist.
- **If missing:** the CI4 install is incomplete. Stop and tell the user to reinstall the framework.

### 2. Composer manifest
- **Rule:** `composer.json` must exist and contain `"codeigniter4/framework"` in `require`.
- **If missing:** confirm the project is actually CI4.

### 3. PHP version constraint
- **Rule:** `composer.json` → `require.php` should be `^8.1` or higher.
- **If lower:** warn. Deploy Now supports PHP 8.1, 8.2, 8.3, 8.4. Recommend 8.2.

### 4. `.gitignore` hygiene
- **Rule:** must exclude `vendor/`, `.env`, `writable/cache/*`, `writable/logs/*`, `writable/session/*`, `writable/debugbar/*`.
- **If missing:** add the entries before committing.

### 5. `app.baseURL` is environment-driven
- **Rule:** `app/Config/App.php` should not hardcode `http://localhost:8080/`. The default CI4 line `public string $baseURL = 'http://localhost:8080/';` should be overridable via env. Ensure `Config\App` reads `app.baseURL` from `.env`.
- **If hardcoded:** advise the user to remove the hardcoded fallback or make it conditional.

### 6. `writable/` tree
- **Rule:** the four subdirectories `cache/`, `logs/`, `session/`, `uploads/` must exist (CI4 creates them automatically; ensure each contains a `.gitkeep`).
- **If missing:** recreate.

### 7. `.deploy-now/<project>/config.yaml`
- **Rule:** must exist after Deploy Now generates the project. The agent provides the template under `templates/deploy-now-config-v2.yaml`.

### 8. `.deploy-now/<project>/.env.template`
- **Rule:** must use `$IONOS_DB_*`, `$IONOS_APP_URL`, `$IONOS_MAIL_*` placeholders (workflow v2).

### 9. `.deploy-now/<project>/.htaccess.template`
- **Rule:** root `.htaccess` redirects to `public/index.php`. A second template at `.deploy-now/<project>/public/.htaccess.template` handles asset rewrites.

### 10. Publish directory in Deploy Now dashboard
- **Rule:** must be set to `public`. This is configured in the Deploy Now wizard, not in the repo.
- **Verify:** ask the user to confirm in the dashboard.

## Output

Emit a Markdown table:

```
| # | Check | Status |
|---|---|---|
| 1 | public/index.php exists | ✅ |
| 2 | composer.json requires codeigniter4/framework | ✅ |
| 3 | PHP ≥ 8.1 | ✅ |
| 4 | .gitignore excludes writable/cache, vendor, .env | ⚠️ missing writable/cache/* |
| 5 | app.baseURL env-driven | ✅ |
| 6 | writable/ subdirs present | ✅ |
| 7 | .deploy-now/<project>/config.yaml | ❌ to be generated |
| 8 | .deploy-now/<project>/.env.template | ❌ to be generated |
| 9 | .deploy-now/<project>/.htaccess.template | ❌ to be generated |
| 10 | Publish dir = public/ in dashboard | (manual confirmation) |
```
