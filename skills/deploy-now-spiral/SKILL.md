---
name: deploy-now-spiral
description: Deploy Spiral Framework applications to IONOS Deploy Now. Spiral is NOT officially detected by Deploy Now and uses RoadRunner (not PHP-FPM), so it is INCOMPATIBLE with Deploy Now's shared hosting model. This skill documents the incompatibility and recommends alternatives. Use when the user asks about deploying Spiral to Deploy Now — the agent should warn that Spiral requires RoadRunner and long-running processes, which Deploy Now does not support.
metadata:
  author: liusc45
  category: deployment
  workflow_version: v2
---

# Deploy Spiral to IONOS Deploy Now

> **⚠️ INCOMPATIBLE.** Spiral requires **RoadRunner** (a long-running application server written in Go), not PHP-FPM or Apache. Deploy Now provides **shared Apache hosting with PHP-FPM** — it does not support custom binaries, long-running processes, or RoadRunner. **Spiral cannot be deployed to Deploy Now.**

## When to use

- "deploy Spiral to IONOS" → **warn user of incompatibility**
- "Deploy Now Spiral setup" → **recommend alternative hosting**

## Why it does not work

| Spiral requirement | Deploy Now capability |
|---|---|
| RoadRunner binary (`./rr serve`) | No custom binaries allowed |
| Long-running process | No persistent processes; only cron jobs |
| Custom server (`app.php` bootstrapper) | Apache + PHP-FPM only |
| gRPC / WebSocket support | HTTP only |

## Recommended alternatives for Spiral

- **DigitalOcean App Platform** or **Droplet** — install RoadRunner manually
- **Hetzner Cloud** — VPS with full control
- **Railway** — supports custom Docker images
- **Any VPS / dedicated server** — install RoadRunner + PHP 8.2 CLI

## If the user insists on IONOS

Consider IONOS VPS (not Deploy Now) — a full Linux VM where RoadRunner can run. Contact IONOS sales for VPS options.

## References

- Spiral deployment docs: https://spiral.dev/docs/start/deployment
- RoadRunner: https://roadrunner.dev/
