<div align="center">

# Easy2FA

![Easy2FA](assets/cover.png)

### _The tool that keeps your secrets should be entirely your own._

**A stateless, self-hosted TOTP (2FA) code generator — no backend, no database, no tracking. Your secret never leaves your browser.**

**⚡ Dead-simple to deploy · 🆓 Free forever · 🧰 Zero maintenance**

[![GitHub stars](https://img.shields.io/github/stars/zeropl/2FA?style=flat&logo=github&color=22d3ee&labelColor=06090c)](https://github.com/zeropl/2FA/stargazers)
[![License: MIT](https://img.shields.io/badge/license-MIT-22d3ee?labelColor=06090c)](LICENSE)
[![Build: none](https://img.shields.io/badge/build-none-22d3ee?labelColor=06090c)](#deployment)
[![Backend: none](https://img.shields.io/badge/backend-none-22d3ee?labelColor=06090c)](#how-it-works)
[![Self-tests: 98](https://img.shields.io/badge/self--tests-98%20passing-22d3ee?labelColor=06090c)](tests.html)
[![CI](https://github.com/zeropl/2FA/actions/workflows/tests.yml/badge.svg)](https://github.com/zeropl/2FA/actions/workflows/tests.yml)

English · [简体中文](README.zh-CN.md)

</div>

---

## 🚀 One-click deploy

> Pure static, **no build step**. Each button **clones this repo into your own GitHub account and deploys it** — so you get your own free, auto-updating copy. Config files in the repo (`wrangler.jsonc` / `netlify.toml` / `vercel.json`) make every deploy zero-config.

[![Deploy to Cloudflare](https://deploy.workers.cloudflare.com/button)](https://deploy.workers.cloudflare.com/?url=https://github.com/zeropl/2FA)
[![Deploy to Netlify](https://www.netlify.com/img/deploy/button.svg)](https://app.netlify.com/start/deploy?repository=https://github.com/zeropl/2FA)
[![Deploy with Vercel](https://vercel.com/button)](https://vercel.com/new/clone?repository-url=https://github.com/zeropl/2FA)

Prefer **GitHub Pages**, or want to fork first? See the detailed **[Deployment](#deployment)** guide below.

---

## Why yet another 2FA tool?

Test accounts pile up — CI bots, staging back-offices, crawler accounts, that demo login the whole team shares — and nowadays every one of them demands 2FA. Stuff them into your phone's authenticator? They end up sandwiched between your bank codes, migrate painfully with every new phone, and "can I borrow that account?" means literally handing your phone over. Put them in a password manager? That's renting a bank vault for paper clips.

Easy2FA turns "an account" back into "a link":

- **One bookmark = one account.** Open it — the code is already ticking.
- **Send the link = handover done.** Your teammate opens it and reads the live code. Nothing to install.
- **Drop a file = a wall of codes.** Drag your `.txt` list onto the page and every code renders in one live grid.

No account system, no app, no sync — which also means no "new phone, everything's gone", no forgotten master password, no service shutting down. It's a **folder of static files**: host it anywhere, and it'll still run ten years from now.

## Three things it refuses to compromise on

2FA tools are everywhere. Easy2FA earns its place by taking three stances most tools can't be bothered with:

### 1. The best storage is no storage

Other tools promise to "store your secrets safely". Easy2FA's answer: **it doesn't store them at all.** No server (it's static files), no sign-up, nothing kept in the browser — your bookmarks and link files are the only source of truth.

The one exception is the multi-account board, and even that is just a "recently opened"-style local cache: clear it in one click, restore it by re-dropping your file. It never takes custody. Backups, encryption, recovery, breach response — the whole chain of problems that comes with having state simply doesn't exist here.

### 2. Secrets never leave — and the browser enforces it

Anyone can claim "we don't upload your data". Easy2FA makes **the browser verify the claim**:

- The secret lives in the URL's `#` fragment — the HTTP protocol itself **never** sends fragments with requests;
- The page ships a **CSP (`connect-src 'self'`)** — even if a dependency were ever poisoned, the browser blocks any request to an external host on the spot;
- React & friends are vendored into the repo, no CDN. Open the DevTools Network panel: every single request points at your own domain.

### 3. Never confidently wrong

For a 2FA tool, **calmly displaying a wrong code is worse than crashing** — you'll retry it until the account locks you out. So:

- The algorithm is pinned by the official RFC 6238 test vectors, and the **98 self-tests in [`tests.html`](tests.html) execute the production code itself** — extracted straight out of `index.html`, so tests and implementation cannot drift, and CI runs them headlessly on every push;
- Unsupported means refused: HOTP links are **rejected outright** (not silently computed as TOTP), and unknown algorithms / digit counts in migration QRs are **dropped** (not guessed at);
- On load, it compares your device clock against the server's `Date` response header and **warns loudly** when it's ≥10s off. Clock skew is the most invisible way TOTP fails — and most tools stay silent about it.

## Why / when to use it

- 🧮 **Watch many accounts on one screen.** Drag a `.txt` / `.md` / `.yaml` file of account links onto the page (or paste them, or scan a **Google Authenticator export QR**) → a live, color-coded grid of every code at once. Your file stays the source of truth; the board only caches it locally and can be cleared anytime.
- 🗂️ **You manage many test accounts.** Save each one as a bookmark (`…/#secret=…&label=acme-test`). Click a bookmark → instantly see that account's current code. No authenticator app, no account list to maintain, nothing to sync. A "Test Accounts" bookmark folder becomes your dashboard.
- 🤝 **Temporarily lend an account to a teammate / friend.** Send them the account's link; they open it and read the live code to sign in — without sharing your password-manager credentials or making them install an authenticator. *(The link carries the secret — share it over a trusted channel and rotate afterward.)*
- ⚡ **One-off code.** Paste a secret on the home screen, read the code, close the tab. Stateless by design.
- 📱 **Move a secret to your phone.** "Show QR" renders an `otpauth://` QR you can scan straight into Google Authenticator / Authy / any app.
- 🖥️ **Show a code on a shared screen.** Append `&present=1` to any account link (or hit "🖥 Presentation") for a big, projector-friendly code with the QR and other controls hidden — screen-share or let someone read it without the secret QR ever appearing. *(On-screen protection only — the link still contains the secret and does not hide it from whoever you send it to.)*

## Features

- 🔐 **Real TOTP** (RFC 6238) computed locally via Web Crypto — SHA-1/256/512, configurable digits & period
- 🔗 **Every account is a link** — `#secret=…` lives in the URL hash, never sent to any server
- 🧮 **Multi-account board** — drop a file of links (or scan a Google Authenticator migration QR) → a color-coded grid of live codes; export back to a `.txt` list or a single board link
- 📷 **QR import** (scan a QR image) & **QR export** (`otpauth://` for your phone)
- 🖥️ **Presentation mode** (`&present=1`) — shows just the big rotating code with the QR / secret-revealing UI hidden, for projecting or screen-sharing; honest about what it does (and doesn't) protect
- 🕰 **Clock check** — warns loudly when your device clock is skewed, instead of letting you hammer a wrong code
- 🌐 **Bilingual UI** — one-tap 中/EN switch, auto-detected from your browser language (`?lang=` / localStorage override)
- 🛡 **CSP-fenced** — `connect-src 'self'`: "secrets never leave" is enforced by the browser
- 📲 **PWA** — installable to your home screen, works fully offline; deployed updates reach visitors automatically (stale-while-revalidate)
- ♿ **Accessible** — fully keyboard-operable, respects reduced-motion, AA contrast
- 🚫 **No backend, no server-side storage, no telemetry** — secrets never touch a server (the board's cache is local-only and clearable)
- 🪶 **Tiny & portable** — static files + vendored React; deploys anywhere

## Screenshots

<p align="center">
  <img src="assets/screenshot-view.png" width="31%" alt="Live code view" />
  <img src="assets/screenshot-setup.png" width="31%" alt="Add a secret" />
  <img src="assets/screenshot-qr.png" width="31%" alt="QR export" />
</p>
<p align="center"><sub>Live code &amp; countdown · Paste a secret → instant code + bookmark link · Export as <code>otpauth://</code> QR</sub></p>

## How it compares

|  | Phone authenticator | Password manager | **Easy2FA** |
|---|---|---|---|
| Best for | Your own important accounts | The team's real credentials | **Test / shared / throwaway accounts** |
| New device | Migration ritual | Install + sign in | **Open a bookmark** |
| Lend to a teammate | Hand over your phone | Invite them into the vault | **Send a link** |
| Where the data lives | Phone + vendor cloud | Vendor's servers | **Your bookmarks / your files** |
| Can you read all the code | Usually closed-source | Usually closed-source | **One HTML file** |
| High-value accounts | ✅ Use these | ✅ Use these | ❌ **Don't. Really.** |

The three columns aren't rivals — that last row is sincere: banks, primary email and production credentials belong with hardware keys or a dedicated authenticator. Easy2FA herds the big pile of small accounts they'd never bother with.

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

If you want that real server without setting one up, this deploys the repo to a Linux box with nginx and HTTPS already in place:

[![Deploy on InstaPods](https://instapods.com/deploy-button.svg)](https://app.instapods.com/dashboard/pods/create?repo=https://github.com/zeropl/2FA&ref=gh-easy2fa)

> **Updating a deployed copy:** nothing to do. You push → the platform redeploys → visitors' service workers fetch the new version in the background, and their next refresh runs it. (The `CACHE` name in [`sw.js`](sw.js) now only matters when you want to wipe the old cache pool entirely.)

## How it works

- The 6-digit code is computed in-browser with `crypto.subtle` (HMAC) — standard RFC 6238 TOTP.
- The secret is read from `location.hash`. **URL fragments are not included in HTTP requests**, so the secret never leaves your device over the network — even on a hosted deploy, the server only ever sees a request for a static file.
- React / ReactDOM are **vendored** under `vendor/` (no CDN dependency → loads even on flaky networks, and is integrity-pinned).
- The page ships a **CSP** (`connect-src 'self'`) — even if a dependency were ever compromised, the browser itself blocks any request to an external host. “Secrets never leave” is browser-enforced, not just a promise.
- On load it **checks your clock** against the same-origin `Date` response header and warns loudly when it's ≥10s off — a skewed clock is the most common invisible way TOTP goes wrong.

### Want to audit it? One afternoon is enough

A tool that touches secrets deserves to be read before it's trusted. Easy2FA keeps that as easy as it gets:

- All application logic lives in **one [`index.html`](index.html)** (~1,500 lines — UI templates and both languages included);
- **No build step, no `node_modules`** — every line you read on GitHub is exactly what your browser executes;
- The entire supply chain is four files under `vendor/`: React, ReactDOM (official UMD builds) and two QR libraries (qrcode.js to generate, jsQR as the decode fallback);
- Open `/tests.html` on your own deployment and **run all 98 self-tests on the spot** — it fetches and tests the very `index.html` you're using.

## Security notes

Easy2FA is built for **test / throwaway accounts** and trades some security for convenience. Know the tradeoffs:

- The secret lives **in the URL**, so it ends up in your **browser history and bookmarks** — and is **synced to the cloud** if your browser syncs bookmarks/history. Treat the links as sensitive.
- Anyone you hand a link to gets the secret. Share over trusted channels; rotate after temporary sharing.
- For **high-value accounts**, use a hardware key or a dedicated authenticator app instead.
- Requires **HTTPS** (Web Crypto needs a secure context). `localhost` counts for local dev; `file://` does not.
- The **multi-account board** stores its list in your browser's **localStorage** (a local cache — never synced anywhere by the app). Hit **Clear** to wipe it; your own file remains the real backup.
- **Presentation mode** (`&present=1`) only guards against **on-screen** exposure of *your* session (shoulder-surfing / screen-share / recording) by hiding the QR. It does **not** protect the recipient of a link — the URL still contains the secret. It is not "secure sharing".

## ⭐ If it saved you a trip to your phone

Easy2FA has no cloud service, no Pro tier, and collects nothing — **stars are its entire revenue model**. If it ever spared you some authenticator-app archaeology, feed it one ⭐.

[![Star History Chart](https://api.star-history.com/svg?repos=zeropl/2FA&type=Date)](https://star-history.com/#zeropl/2FA&Date)

## Changelog

- **2026-07** — **Per-card board actions + QR scanning in every browser + CI**: every board card now has **↗ open single view / 🔗 copy account link / ✕ remove** (two-step confirm) — no more “clear everything to delete one account”, and each card can now reach the single-view QR export; QR scanning no longer requires Chromium's `BarcodeDetector` — a vendored **jsQR** fallback (lazy-loaded, same-origin, SW-precached for offline) makes scanning work in **Safari / Firefox too** (incl. GA migration codes), and also retries when the native engine misses; new **GitHub Actions CI** runs all 98 `tests.html` self-tests headlessly on every push and verifies the `support.js` vendor patch is intact (see PATCHES.md).
- **2026-07** — **Import now actually reads JSON / CSV**: the file picker had always advertised `.csv` / `.json`, but the parser only scraped URLs — feed it a real JSON / CSV export and you got “no accounts recognized” ([issue #5](https://github.com/zeropl/2FA/issues/5)). Structured parsing is now real — JSON accepts an array / a single object / a one-level array wrapper, with case-insensitive keys and common aliases (`name`/`account`→label, `service`→issuer, `timer`→period, `algo`→algorithm); CSV keys off a `secret` header column and handles comma / semicolon / tab delimiters plus quoted commas, while ordinary comma text without a `secret` header is not mistaken for a table. 15 new parser tests (98 total).
- **2026-07** — **Clock check + auto-update + CSP**: on load, the device clock is compared against the same-origin `Date` response header, with a loud warning when it's ≥10s off (closing the last “confidently wrong code” path); the service worker now uses **stale-while-revalidate**, so deployed updates reach visitors automatically — no more manual cache-version bumps; added a **CSP** (`connect-src 'self'`) that makes “secrets never leave” browser-enforced; `tests.html` was rebuilt to **extract the real implementation straight out of `index.html`** (zero mirror drift) with new parser-layer tests (78 checks). Same batch: fixed the stale `#secret=` hash after “Add to board”, iOS home-screen icon, a warning for over-long board links, and autocomplete off on secret inputs.
- **2026-06** — **Bilingual UI + a hardening pass**: one-tap 中/EN switch (top-right), auto-detected from browser language / `?lang` / localStorage. Same batch: reject HOTP links and invalid/truncated bookmark links (no silently-wrong codes), accessibility (keyboard copy / reduced-motion / focus rings / AA contrast), lazy-loaded `qrcode.js`, plus a no-build `tests.html` (RFC 6238 self-test) and `PATCHES.md`.
- **2026-06** — **Zero-config deploy**: shipped `wrangler.jsonc` / `netlify.toml` / `vercel.json` / `.nojekyll` and a fork-first deploy guide, so deploying to any platform is just "authorize → pick your fork → deploy" with no build settings. Corrected the Cloudflare button (it now creates a Worker with static assets, not Pages).
- **2026-06** — **Presentation mode** (`&present=1`, optional `&nolabel=1`): a big, projector-friendly code with the QR and editing controls hidden, for screen-sharing without exposing the secret QR. Honest scope — it guards your screen, not the link's recipient.
- **2026-06** — **Multi-account board**: import a list file / paste / clipboard / Google Authenticator migration QR → a live, color-coded grid of codes (one timer drives them all). Export back to a `.txt` list or a single `#board=…` link. The board keeps an optional, one-click-clearable local cache — your file stays the source of truth.
- **2026-06** — Initial release: stateless single-account view, paste-a-secret setup with bookmark links, QR import / export, PWA + offline.

## License

[MIT](LICENSE)
