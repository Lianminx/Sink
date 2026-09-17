# Lianmin Sink deployment

- Dashboard: https://lianmin-links.uptimeworker.workers.dev/dashboard
- Example link: https://lianmin-links.uptimeworker.workers.dev/blog
- Personal configuration: `wrangler.personal.jsonc` (use this instead of the upstream deployment configuration).
- Storage: D1 `lianmin-sink` and KV `lianmin-sink`.
- Free-only deployment: no paid plan, custom domain, R2, Workers AI, Analytics Engine, or backup cron.
- Link management, redirects and local JSON export are available. Analytics charts, AI suggestions, image uploads and R2 backups are not configured.

## Authentication

The administrator token is stored as the Worker secret `NUXT_SITE_TOKEN`. Its local copy is in `.dev.secrets.json`, which is ignored by Git. Never commit or publish it. Enter this token in the dashboard login form. Cloudflare credentials are managed separately by Wrangler.

## Build and publish

Use Node.js 24+ and pnpm 11.11.0. On Windows, set `NODE_OPTIONS=--max-old-space-size=8192` in PowerShell, then run:

```powershell
pnpm install --frozen-lockfile
pnpm build:sphere
pnpm exec nuxt build
pnpm exec wrangler d1 migrations apply DB --remote --config wrangler.personal.jsonc
pnpm exec wrangler deploy --config wrangler.personal.jsonc
```

For a new instance, complete `/api/link/migration/run` with authenticated POST before creating links, even when KV is empty. The deployed instance completed this initialization on 2026-09-17.

Commit and push changes to the personal fork. GitHub pushes do not automatically deploy. Keep upstream workflows disabled unless explicitly configured for this instance.

## Verification

On 2026-09-17: dashboard returned 200, authenticated verification returned 200, an unauthenticated API call returned 401, and `/blog` redirected to the blog with HTTP 301. These requests succeeded through the local proxy; direct workers.dev access from this machine failed. Browser usability still depends on the user's network.
