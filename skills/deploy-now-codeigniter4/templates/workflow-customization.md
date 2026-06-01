# Snippets to add to .github/workflows/<project>-build.yaml
# (Deploy Now generates the file; this customization is applied AFTER generation.)
# Workflow v2.
# Source: https://docs.ionos.space/docs/github-actions-customization/

# --- A. Setup PHP --------------------------------------------------
# Replace any auto-generated PHP setup step with this exact block.
      - name: Setup PHP
        uses: shivammathur/setup-php@v2
        with:
          php-version: '8.2'
          extensions: intl, mbstring, mysqli, pdo_mysql, curl, gd, fileinfo
          coverage: none
          tools: composer:v2

# --- B. Install Composer dependencies ------------------------------
      - name: Install dependencies
        run: composer install --no-dev --optimize-autoloader --no-interaction --prefer-dist

# --- C. (Optional) Build front-end assets --------------------------
# Add only if the project uses Vite/Webpack/Tailwind etc.
      - name: Setup Node
        uses: actions/setup-node@v4
        with:
          node-version: '20'

      - name: Build assets
        run: |
          npm ci
          npm run build

# --- D. Publish directory ------------------------------------------
# Verify this env block at the top of the workflow file.
# DEPLOYMENT_FOLDER must be ./ — the entire repo is uploaded and the
# dashboard "publish directory" setting (public) controls what Apache serves.
env:
  DEPLOYMENT_FOLDER: ./

# --- E. DO NOT MODIFY ----------------------------------------------
# The trailing steps in the generated workflow:
#   - ionos-deploy-now/template-renderer-action@v1
#   - ionos-deploy-now/deploy-to-ionos-action@v1
# These depend on internal Deploy Now outputs and must stay untouched.
