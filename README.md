<div align="center">

# Easy2FA

![Easy2FA](assets/cover.png)

**A stateless, self-hosted TOTP (2FA) code generator. Pure static — no backend, no database, no tracking. Your secret never leaves the browser.**

**无状态、可自部署的 TOTP（两步验证）验证码工具。纯静态——无后端、无数据库、无追踪，密钥永不离开浏览器。**

English · [简体中文](#简体中文)

</div>

---

## What is this?

Easy2FA turns a 2FA secret into its live 6-digit code, entirely in your browser. There's no server, no sign-up, and no database — each "account" is just a **bookmarkable URL** with the secret in its `#fragment` (which browsers never send over the network).

It's deliberately tiny and **stateless**: open it, get a code, close it — nothing is stored. Perfect for juggling **lots of throwaway / test-account 2FA secrets** without an app or sync.

## Why / when to use it

- 🗂️ **You manage many test accounts.** Save each one as a bookmark (`…/#secret=…&label=acme-test`). Click a bookmark → instantly see that account's current code. No authenticator app, no account list to maintain, nothing to sync. A "Test Accounts" bookmark folder becomes your dashboard.
- 🤝 **Temporarily lend an account to a teammate / friend.** Send them the account's link; they open it and read the live code to sign in — without sharing your password-manager credentials or making them install an authenticator. *(The link carries the secret — share it over a trusted channel and rotate afterward.)*
- ⚡ **One-off code.** Paste a secret on the home screen, read the code, close the tab. Stateless by design.
- 📱 **Move a secret to your phone.** "Show QR" renders an `otpauth://` QR you can scan straight into Google Authenticator / Authy / any app.

## Features

- 🔐 **Real TOTP** (RFC 6238) computed locally via Web Crypto — SHA-1/256/512, configurable digits & period
- 🔗 **Every account is a link** — `#secret=…` lives in the URL hash, never sent to any server
- 📷 **QR import** (scan a QR image) & **QR export** (`otpauth://` for your phone)
- 📲 **PWA** — installable to your home screen, works fully offline
- 🚫 **No backend, no storage, no telemetry** — secrets never touch a server
- 🪶 **Tiny & portable** — static files + vendored React; deploys anywhere

## Screenshots

<p align="center">
  <img src="assets/screenshot-view.png" width="31%" alt="Live code view" />
  <img src="assets/screenshot-setup.png" width="31%" alt="Add a secret" />
  <img src="assets/screenshot-qr.png" width="31%" alt="QR export" />
</p>
<p align="center"><sub>Live code &amp; countdown · Paste a secret → instant code + bookmark link · Export as <code>otpauth://</code> QR</sub></p>

## One-click deploy

> One-click deploy this app to your host of choice (forking? point these at your own fork):

[![Deploy to Cloudflare Pages](https://deploy.workers.cloudflare.com/button)](https://deploy.workers.cloudflare.com/?url=https://github.com/zeropl/2FA)
[![Deploy to Netlify](https://www.netlify.com/img/deploy/button.svg)](https://app.netlify.com/start/deploy?repository=https://github.com/zeropl/2FA)
[![Deploy with Vercel](https://vercel.com/button)](https://vercel.com/new/clone?repository-url=https://github.com/zeropl/2FA)

Or **GitHub Pages**: push the repo, then *Settings → Pages → Deploy from branch → `main` / root*. It works as-is (the app uses relative paths and a `./` service-worker scope, so it runs fine under a sub-path).

There's **no build step** — it's pure static. Publish directory = repo root, build command = *(none)*. HTTPS is required (Web Crypto needs a secure context); every platform above provides it automatically.

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

## Tech notes

- No build step. The app is `index.html` + `support.js` (a generated `dc-runtime`) + `vendor/`.
- **Service Worker cache:** after changing any static file and redeploying, bump `CACHE` in [`sw.js`](sw.js) (e.g. `2fa-v3` → `2fa-v4`) so returning visitors actually get the update.

## License

[MIT](LICENSE)

---

<a name="简体中文"></a>

# Easy2FA · 简体中文

![Easy2FA](assets/cover.png)

**无状态、可自部署的 TOTP（两步验证）验证码工具。纯静态——无后端、无数据库、无追踪，密钥永不离开浏览器。**

## 这是什么？

Easy2FA 在你的浏览器里把一个 2FA 密钥算成实时的 6 位验证码。没有服务器、不用注册、没有数据库——每个"账号"就是一条**可收藏的网址**，密钥藏在它的 `#` 片段里（浏览器永远不会把 `#` 片段发到网络上）。

它刻意做得极小、**完全无状态**：打开、看码、关掉，什么都不留。特别适合在不装 App、不做同步的情况下，管理**一大堆一次性 / 测试号的 2FA 密钥**。

## 用来做什么 / 什么场景用

- 🗂️ **管理很多测试号。** 把每个号存成一条书签（`…/#secret=…&label=acme-test`）。点一下书签 → 立刻看到该号当前的验证码。不用装验证器 App、不用维护账号列表、不用同步。一个"测试号"书签文件夹就是你的总览面板。
- 🤝 **把账号临时借给同事 / 好友用一下。** 把该账号的链接发给对方，对方打开就能读到实时验证码登录——既不用把你的密码管理器凭据给他，也不用让他装验证器。*（链接里带着密钥，请通过可信渠道发送，用完及时轮换密钥。）*
- ⚡ **临时算一次码。** 在首页粘贴密钥、读出验证码、关掉标签页。天生无状态，什么都不存。
- 📱 **把密钥导到手机。** 点"显示二维码"，生成 `otpauth://` 二维码，直接扫进 Google Authenticator / Authy / 任意验证器 App。

## 功能

- 🔐 **标准 TOTP**（RFC 6238），用 Web Crypto 本地计算——支持 SHA-1/256/512、可配位数与周期
- 🔗 **每个账号就是一条链接**——`#secret=…` 在 URL hash 里，绝不发往任何服务器
- 📷 **二维码导入**（扫一张二维码图）& **二维码导出**（`otpauth://`，方便导到手机）
- 📲 **PWA**——可添加到主屏、完全离线可用
- 🚫 **无后端、无存储、无埋点**——密钥永不接触服务器
- 🪶 **小巧可移植**——纯静态文件 + 内置 React，哪里都能部署

## 截图

<p align="center">
  <img src="assets/screenshot-view.png" width="31%" alt="实时验证码" />
  <img src="assets/screenshot-setup.png" width="31%" alt="录入密钥" />
  <img src="assets/screenshot-qr.png" width="31%" alt="二维码导出" />
</p>
<p align="center"><sub>实时验证码与倒计时 · 粘贴密钥即出码 + 生成专属书签链接 · 导出为 <code>otpauth://</code> 二维码</sub></p>

## 一键部署

> 一键把本应用部署到你常用的平台（如果是 fork，把链接指向你自己的 fork）：

[![Deploy to Cloudflare Pages](https://deploy.workers.cloudflare.com/button)](https://deploy.workers.cloudflare.com/?url=https://github.com/zeropl/2FA)
[![Deploy to Netlify](https://www.netlify.com/img/deploy/button.svg)](https://app.netlify.com/start/deploy?repository=https://github.com/zeropl/2FA)
[![Deploy with Vercel](https://vercel.com/button)](https://vercel.com/new/clone?repository-url=https://github.com/zeropl/2FA)

或者用 **GitHub Pages**：推送仓库后，进 *Settings → Pages → Deploy from branch → `main` / 根目录* 即可（应用全程用相对路径、Service Worker 作用域为 `./`，在子路径下也能正常跑）。

**没有构建步骤**——纯静态。发布目录 = 仓库根目录，构建命令 = *（无）*。需要 **HTTPS**（Web Crypto 要求安全上下文）；上面几个平台都自带 HTTPS。

## 工作原理

- 6 位验证码由浏览器用 `crypto.subtle`（HMAC）本地算出——标准 RFC 6238 TOTP。
- 密钥从 `location.hash` 读取。**URL 的 `#` 片段不会进入 HTTP 请求**，所以密钥永不经网络离开你的设备——即便部署在线上，服务器看到的也只是一次静态文件请求。
- React / ReactDOM 已 **vendoring** 到 `vendor/`（不依赖 CDN → 网络差也能开，且做了完整性校验）。

## 安全须知

Easy2FA 是为**测试 / 一次性账号**设计的，用一部分安全性换取便利。请了解其中取舍：

- 密钥在 **URL 里**，因此会进入你的**浏览器历史和书签**——如果浏览器开了书签/历史云同步，它也会**被同步到云端**。请把这些链接当作敏感信息。
- 你把链接发给谁，谁就拿到了密钥。请走可信渠道；临时分享后请轮换密钥。
- **重要 / 高价值账号**请改用硬件密钥或专门的验证器 App。
- 需要 **HTTPS**（Web Crypto 要求安全上下文）。本地开发用 `localhost` 可以；`file://` 不行。

## 技术说明

- 无构建步骤。应用由 `index.html` + `support.js`（生成的 `dc-runtime`）+ `vendor/` 组成。
- **Service Worker 缓存：** 改动任何静态文件并重新部署后，记得提升 [`sw.js`](sw.js) 里的 `CACHE` 版本号（如 `2fa-v3` → `2fa-v4`），否则老访客拿不到更新。

## 许可

[MIT](LICENSE)
