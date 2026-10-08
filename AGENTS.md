# AGENTS.md

## What this project is
A single, fully self-contained static page: `index.html` (Portuguese, `pt-br`).
No `package.json`, no build step, no bundler, no backend, no tests.

Non-obvious details worth knowing before editing:
- The two character images are **inline base64 data URIs** — there are no asset
  files to add and no image requests to debug.
- There is **no JavaScript** in the page; all styling is inline in a `<style>` block.
- No external URLs at all (no CDNs, fonts or APIs), so it works fully offline and
  needs **no environment variables, no secrets and no database**.

## Running it
```bash
docker compose -f docker-compose.base44.yml up -d --build
```
`web` (nginx:alpine) serves the repository root read-only as its document root on
host port **3000**. There is nothing to install, migrate or seed.

Sandbox-only detail: `.base44/nginx.conf` is mounted over `/etc/nginx/nginx.conf`.
The platform checks the repo out with a **mode-700 root directory**, which nginx's
default unprivileged worker user cannot traverse — without that config every
request returns 403. It only changes the worker user; the served site is
untouched and a normal deployment would use the image's stock config.

## Verifying it works
```bash
curl -s http://localhost:3000/ | grep -q "Poppy Playtime" && echo OK
docker compose -f docker-compose.base44.yml ps   # web should be healthy
```

## Edit loop
nginx always serves the current file from disk, so an edit to `index.html` is
visible on refresh — but there is no HMR/live-reload dev server, so call
`reload_preview` after editing to refresh the preview.
