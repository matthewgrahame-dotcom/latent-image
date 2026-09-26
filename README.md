# Latent Image — deploy to Railway

A self-contained PWA: point your camera at a scene, get matched historic
photographs with real composition analysis, and "recreate this look"
guidance. Zero external dependencies — one Node file serves static assets.

## What's in this folder

```
latent-image/
├── public/
│   ├── index.html         the whole app (HTML/CSS/JS, self-contained)
│   ├── manifest.json       PWA manifest (name, icons, display mode)
│   ├── service-worker.js   offline caching + install support
│   ├── icon-192.png
│   ├── icon-512.png
│   └── images/             40 real archive photographs, pulled out of
│                            the claude.ai artifact so they work as a
│                            standalone site
├── server.js               dependency-free static file server
├── package.json
└── .gitignore
```

## Deploy it — fastest path (Railway CLI, no GitHub needed)

1. Install Node.js if you don't already have it (you do, from the earlier
   scraping scripts).
2. Install the Railway CLI:
   ```
   npm install -g @railway/cli
   ```
3. From inside this folder:
   ```
   railway login
   railway init
   railway up
   ```
   `railway login` opens your browser to authenticate. `railway init`
   creates a new Railway project (accept the defaults). `railway up`
   uploads and deploys this folder directly — no Git required.
4. Once it finishes, run `railway domain` to generate a public
   `*.up.railway.app` URL (or add one from the Railway dashboard under
   your service's **Settings → Networking → Generate Domain**).
5. Open that URL on your phone. Because it's a real standalone site (not
   an embedded artifact view), "Add to Home Screen" will offer a proper
   install with its own icon, and camera permission works the normal way
   any site's does — the in-app-browser issue you hit before shouldn't
   come up here.

## Deploy it — better for ongoing updates (GitHub-linked)

1. Push this folder to a new GitHub repo (same flow as your other
   projects — `git init`, `git add .`, `git commit`, create the repo on
   GitHub, `git push`).
2. In the Railway dashboard: **New Project → Deploy from GitHub repo** →
   select the repo.
3. Railway auto-detects the Node app from `package.json` and deploys it.
   No build configuration needed — `npm start` runs `server.js`.
4. Generate a public domain the same way as above.
5. From now on, every `git push` redeploys automatically.

## Updating the app later

If you want to add more photographs or tweak anything, the easiest flow
is: keep working on the version in the claude.ai artifact (asset uploads,
iterative edits are much easier there), then when you're ready to publish
a new version, re-export it the same way this one was built — pull any
new image assets out as local files, copy the updated HTML into
`public/index.html`, and redeploy.

## Notes

- `service-worker.js` caches images aggressively (`immutable`) since
  they never change once deployed, but always re-fetches `index.html`
  and the service worker itself so updates show up without anyone
  needing to uninstall and reinstall.
- No environment variables are required. Railway sets `PORT`
  automatically and `server.js` reads it.
- Total image payload is about 22 MB — fine for Railway, but worth
  knowing if you later want to compress the largest files (a couple are
  several MB from high-resolution archive scans).
