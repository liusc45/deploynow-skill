# Contributing

Thanks for your interest in improving `deploynow-skill`.

## Ground rules

1. **Stick to official sources.** All guidance must be traceable to:
   - [docs.ionos.space](https://docs.ionos.space/)
   - [github.com/ionos-deploy-now/laravel-starter](https://github.com/ionos-deploy-now/laravel-starter)
   - [github.com/ionos-deploy-now/symfony-starter](https://github.com/ionos-deploy-now/symfony-starter)
   - [github.com/ionos-deploy-now/deploy-to-ionos-action](https://github.com/ionos-deploy-now/deploy-to-ionos-action)
2. **Mark inference clearly.** CodeIgniter 4 is not officially supported by Deploy Now. Anything CI4-specific must be labeled as a "reasonable inference" derived from generic PHP support.
3. **English only** in all files (frontmatter, body, comments).
4. **YAML frontmatter must be valid.** `name` and `description` are required.

## Project layout

```
skills/<skill-name>/
├── SKILL.md            # Required entry point with frontmatter
├── workflows/          # Step-by-step internal procedures the agent follows
├── templates/          # Files the agent copies into the user's project
├── references/         # Background docs the agent reads when needed
└── examples/           # End-to-end walkthroughs
```

## Local validation

Before submitting a PR, validate frontmatter:

```bash
# Check that every SKILL.md has name and description
for f in skills/*/SKILL.md; do
  echo "=== $f ==="
  head -10 "$f"
done
```

Test discovery from your branch:

```bash
npx skills add <your-fork>/deploynow-skill --list
```

## PRs

- Open a single-purpose PR per skill or per template change.
- Include a link to the doc page or starter file you used as the source.
- Update `docs/CHANGELOG.md`.

## License

By contributing, you agree your contributions are licensed under the [MIT License](../LICENSE).
