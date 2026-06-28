<div align="center">

# Easy2FA

![Easy2FA](assets/cover.png)

### _The tool that keeps your secrets should be entirely your own._

**A stateless, self-hosted TOTP (2FA) code generator — no backend, no database, no tracking. Your secret never leaves your browser.**

**⚡ Dead-simple to deploy · 🆓 Free forever · 🧰 Zero maintenance**

[简体中文](README.md) · English

</div>

---

## 🚀 One-click deploy

> Pure static, **no build step**. Each button **clones this repo into your own GitHub account and deploys it** — so you get your own free, auto-updating copy. Config files in the repo (`wrangler.jsonc` / `netlify.toml` / `vercel.json`) make every deploy zero-config.

[![Deploy to Cloudflare](https://deploy.workers.cloudflare.com/button)](https://deploy.workers.cloudflare.com/?url=https://github.com/zeropl/2FA)
[![Deploy to Netlify](https://www.netlify.com/img/deploy/button.svg)](https://app.netlify.com/start/deploy?repository=https://github.com/zeropl/2FA)
[![Deploy with Vercel](https://vercel.com/button)](https://vercel.com/new/clone?repository-url=https://github.com/zeropl/2FA)

Prefer **GitHub Pages**, or want to fork first? See the detailed **[Deployment](#deployment)** guide below.

---

## What is this?

Easy2FA turns a 2FA secret into its live 6-digit code, entirely in your browser. There's no server, no sign-up, and no database — each "account" is just a **bookmarkable URL** with the secret in its `#fragment` (which browsers never send over the network).

At its core it's deliberately tiny and **stateless**: open a link, get a code, close it — nothing stored. Need to watch many accounts at once? The optional **multi-account board** keeps a local, one-click-clearable cache, with your own list file as the source of truth. Perfect for juggling **lots of throwaway / test-account 2FA secrets** without an app or sync.

## Why / when to use it

- 🧮 **Watch many accounts on one screen.** Drag a `.txt` / `.md` / `.yaml` file of account links onto the page (or paste them, or scan a **Google Authenticator export QR**) → a live, color-coded grid of every code at once. Your file stays the source of truth; the board only caches it locally and can be cleared anytime.
- 🗂️ **You manage many test accounts.** Save each one as a bookmark (`…/#secret=…&label=acme-test`). Click a bookmark → instantly see that account's current code. No authenticator app, no account list to maintain, nothing to sync. A "Test Accounts" bookmark folder becomes your dashboard.
- 🤝 **Temporarily lend an account to a teammate / friend.** Send them the account's link; they open it and read the live code to sign in — without sharing your password-manager credentials or making them install an authenticator. *(The link carries the secret — share it over a trusted channel and rotate afterward.)*
- ⚡ **One-off code.** Paste a secret on the home screen, read the code, close the tab. Stateless by design.
- 📱 **Move a secret to your phone.** "Show QR" renders an `otpauth://` QR you can scan straight into Google Authenticator / Authy / any app.
- 🖥️ **Show a code on a shared screen.** Append `&present=1` to any account link (or hit "🖥 演示模式") for a big, projector-friendly code with the QR and other controls hidden — screen-share or let someone read it without the secret QR ever appearing. *(On-screen protection only — the link still contains the secret and does not hide it from whoever you send it to.)*

## Features

- 🔐 **Real TOTP** (RFC 6238) computed locally via Web Crypto — SHA-1/256/512, configurable digits & period
- 🔗 **Every account is a link** — `#secret=…` lives in the URL hash, never sent to any server
- 🧮 **Multi-account board** — drop a file of links (or scan a Google Authenticator migration QR) → a color-coded grid of live codes; export back to a `.txt` list or a single board link
- 📷 **QR import** (scan a QR image) & **QR export** (`otpauth://` for your phone)
- 🖥️ **Presentation mode** (`&present=1`) — shows just the big rotating code with the QR / secret-revealing UI hidden, for projecting or screen-sharing; honest about what it does (and doesn't) protect
- 📲 **PWA** — installable to your home screen, works fully offline
- 🚫 **No backend, no server-side storage, no telemetry** — secrets never touch a server (the board's cache is local-only and clearable)
- 🪶 **Tiny & portable** — static files + vendored React; deploys anywhere

## Screenshots

<p align="center">
  <img src="assets/screenshot-view.png" width="31%" alt="Live code view" />
  <img src="assets/screenshot-setup.png" width="31%" alt="Add a secret" />
  <img src="assets/screenshot-qr.png" width="31%" alt="QR export" />
</p>
<p align="center"><sub>Live code &amp; countdown · Paste a secret → instant code + bookmark link · Export as <code>otpauth://</code> QR</sub></p>

## Deployment

Easy2FA is **pure static** — `index.html` + `support.js` + `vendor/`, **nothing to build**. The only hard requirement is **HTTPS** (the Web Crypto API needs a secure context); every option below provides it automatically (`localhost` also counts for local testing, but `file://` does not).

**The simplest path is the same everywhere: get your own copy, then connect it.**

> **Why fork first?** A fork is *your* copy of the repo. The platform watches it and **auto-redeploys on every push**, you can tweak it freely, and it's all on a free tier with nothing to maintain. The one-click buttons above actually do the fork for you — each clones this repo into your account and deploys that clone. Prefer to fork by hand? Click **Fork** on GitHub first, then use the "import / connect Git" flow on any platform below and pick your fork.

The repo ships per-platform config (`wrangler.jsonc`, `netlify.toml`, `vercel.json`, `.nojekyll`), so there are **no build settings to fill in** — just authorize and deploy.

### GitHub Pages — free, no button needed

1. **Fork** this repo to your account.
2. Your fork → **Settings** → **Pages**.
3. **Source:** "Deploy from a branch" → **Branch:** `main` → **Folder:** `/ (root)` → **Save**.
4. Wait ~1 minute. Live at `https://<your-username>.github.io/2FA/` (the path is case-sensitive — it matches the repo name `2FA`).

> Runs correctly under the `/2FA/` sub-path because the app uses **relative** asset paths and a `./` service-worker scope. An empty [`.nojekyll`](.nojekyll) is included so Pages serves every file verbatim.

### Cloudflare

> Cloudflare retired *Pages* for new projects in 2025 — the button now creates a **Worker that serves your static assets** (same result: a free `*.workers.dev` HTTPS URL).

- **One-click:** the **Deploy to Cloudflare** button above (clones the repo into your account and deploys), **or**
- **Fork + import:** Cloudflare dashboard → **Workers & Pages** → **Create** → **Import a repository** → pick your fork → leave the build settings empty → **Deploy**.

The included [`wrangler.jsonc`](wrangler.jsonc) (`assets.directory: "./"`) makes this zero-config — no framework, no build command, no output directory to set.

### Netlify

- **One-click:** the **Deploy to Netlify** button above (clones + deploys), **or**
- **Fork + import:** Netlify → **Add new project** → **Import an existing project** → **GitHub** → authorize → pick your fork → leave **Build command** and **Publish directory** at their defaults → **Deploy**.

[`netlify.toml`](netlify.toml) pins `publish = "."` (repo root) with no build command. *(Use the button **or** a manual fork+import — not both, or you'll end up with two copies.)*

### Vercel

- **One-click:** the **Deploy with Vercel** button above (clones + deploys), **or**
- **Fork + import:** Vercel → **Add New… → Project** → **Import Git Repository** → pick your fork → **Framework Preset:** `Other`, leave **Build** & **Output** empty → **Deploy**.

[`vercel.json`](vercel.json) pins the `Other` preset (`"framework": null`) with no build step and the repo root as output.

### Self-host (any static server)

It's just a folder of static files — serve it with whatever you like, over **HTTPS**:

```bash
# local dev (localhost is a secure context, so codes work)
npx serve .
# or
python3 -m http.server 8000
```

For a real server use nginx / Caddy / Apache with a TLS certificate. Plain HTTP (non-localhost) or opening `index.html` via `file://` will **not** compute codes — Web Crypto refuses to run outside a secure context.

> **Updating a deployed copy:** after you change any static file and redeploy, bump `CACHE` in [`sw.js`](sw.js) (e.g. `2fa-v5` → `2fa-v6`) so the service worker serves the new version instead of the cached one.

## How it works

- The 6-digit code is computed in-browser with `crypto.subtle` (HMAC) — standard RFC 6238 TOTP.
- The secret is read from `location.hash`. **URL fragments are not included in HTTP requests**, so the secret never leaves your device over the network — even on a hosted deploy, the server only ever sees a request for a static file.
- React / ReactDOM are **vendored** under `vendor/` (no CDN dependency → loads even on flaky networks, and is integrity-pinned).

## Security notes

Easy2FA is built for **test / throwaway accounts** and trades some security for convenience. Know the tradeoffs:

- The secret lives **in the URL**, so it ends up in your **browser history and bookmarks** — and is **synced to the cloud** if your browser syncs bookmarks/history. Treat the links as sensitive.
- Anyone you hand a link to gets the secret. Share over trusted channels; rotate after temporary sharing.
- For **high-value accounts**, use a hardware key or a dedicated authenticator app instead.
- Requires **HTTPS** (Web Crypto needs a secure context). `localhost` counts for local dev; `file://` does not.
- The **multi-account board** stores its list in your browser's **localStorage** (a local cache — never synced anywhere by the app). Hit **Clear** to wipe it; your own file remains the real backup.
- **Presentation mode** (`&present=1`) only guards against **on-screen** exposure of *your* session (shoulder-surfing / screen-share / recording) by hiding the QR. It does **not** protect the recipient of a link — the URL still contains the secret. It is not "secure sharing".

## Changelog

- **2026-06** — **Bilingual UI + a hardening pass**: one-tap 中/EN switch (top-right), auto-detected from browser language / `?lang` / localStorage. Same batch: reject HOTP links and invalid/truncated bookmark links (no silently-wrong codes), accessibility (keyboard copy / reduced-motion / focus rings / AA contrast), lazy-loaded `qrcode.js`, plus a no-build `tests.html` (RFC 6238 self-test) and `PATCHES.md`.
- **2026-06** — **Zero-config deploy**: shipped `wrangler.jsonc` / `netlify.toml` / `vercel.json` / `.nojekyll` and a fork-first deploy guide, so deploying to any platform is just "authorize → pick your fork → deploy" with no build settings. Corrected the Cloudflare button (it now creates a Worker with static assets, not Pages).
- **2026-06** — **Presentation mode** (`&present=1`, optional `&nolabel=1`): a big, projector-friendly code with the QR and editing controls hidden, for screen-sharing without exposing the secret QR. Honest scope — it guards your screen, not the link's recipient.
- **2026-06** — **Multi-account board**: import a list file / paste / clipboard / Google Authenticator migration QR → a live, color-coded grid of codes (one timer drives them all). Export back to a `.txt` list or a single `#board=…` link. The board keeps an optional, one-click-clearable local cache — your file stays the source of truth.
- **2026-06** — Initial release: stateless single-account view, paste-a-secret setup with bookmark links, QR import / export, PWA + offline.

## License

[MIT](LICENSE)
