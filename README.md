# Green Mountain Games

A free browser games site: static landing page, zero-install games, no accounts.
Every game is a self-contained, dependency-free HTML file — open it directly or
serve the repo root locally.

## Structure

```
/                       landing page (this repo's root index.html)
/games/signal-lost/     Signal Lost — the first game
```

Each game lives in its own folder under `games/` with its own `index.html` and
`README.md`. Adding a new game means adding a new folder plus a card on the
landing page — no build step, no shared framework to update.

## Local development

Serve the repo root and everything (landing page + every game) works from one
static file server:

```powershell
powershell -NoProfile -ExecutionPolicy Bypass -File scripts/serve.ps1
# then visit http://localhost:8123/
```

## Hosting on GitHub Pages

This repo is a static site — GitHub Pages serves it as-is from the root. Once
pushed, enable Pages in the repo's Settings → Pages, source = `main` branch,
`/ (root)`.

**Custom domain**: `grnmtngames.com` (owned via GoDaddy). The `CNAME` file at
the repo root already contains it — GitHub just needs matching DNS records set
at the registrar:

| Type  | Host  | Value                     |
|-------|-------|---------------------------|
| A     | `@`   | `185.199.108.153`         |
| A     | `@`   | `185.199.109.153`         |
| A     | `@`   | `185.199.110.153`         |
| A     | `@`   | `185.199.111.153`         |
| CNAME | `www` | `<github-username>.github.io` |

(In GoDaddy's DNS Management, `@` means the apex/root domain, `grnmtngames.com`
itself — add all four A records under that same host.)

GitHub issues HTTPS automatically once DNS resolves correctly (can take a few
hours to propagate) — enable "Enforce HTTPS" in the Pages settings once the
certificate is ready.

## Ads

Ads (if/when added) belong on this landing page only. Every game is served from
its own page under `games/<name>/`, so as long as no ad network script is added
to a game's own `index.html`, gameplay stays ad-free by construction — nothing
in a game needs to know or care that ads exist elsewhere on the site.

## Games

### [Signal Lost](games/signal-lost/)

A single-file browser RPG: fight your way across an open, fog-of-war frontier
to the sound of a synthesized psychedelic-western trip-hop score. See its
[README](games/signal-lost/README.md) for controls, the leaderboard, and
optional global-leaderboard setup via Supabase.
