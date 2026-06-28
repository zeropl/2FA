<div align="center">

# Easy2FA

![Easy2FA](assets/cover.png)

### _存放密钥的工具，理应完全掌握在自己手中。_

**无状态、可自部署的 TOTP（两步验证）验证码工具——无后端、无数据库、无追踪，密钥永不离开你的浏览器。**

**⚡ 极简部署 · 🆓 永久免费 · 🧰 完全免维护**

简体中文 · [English](README.en.md)

</div>

---

## 🚀 一键部署

> 纯静态、**无构建步骤**。每个按钮都会**把本仓库 clone 进你自己的 GitHub 账号再部署**——于是你得到一份自己的、免费且自动更新的副本。仓库里自带各平台配置（`wrangler.jsonc` / `netlify.toml` / `vercel.json`），部署零配置。

[![Deploy to Cloudflare](https://deploy.workers.cloudflare.com/button)](https://deploy.workers.cloudflare.com/?url=https://github.com/zeropl/2FA)
[![Deploy to Netlify](https://www.netlify.com/img/deploy/button.svg)](https://app.netlify.com/start/deploy?repository=https://github.com/zeropl/2FA)
[![Deploy with Vercel](https://vercel.com/button)](https://vercel.com/new/clone?repository-url=https://github.com/zeropl/2FA)

想用 **GitHub Pages**、或想先自己 fork？见下方详细的 **[部署说明](#部署说明)**。

---

## 这是什么？

Easy2FA 在你的浏览器里把一个 2FA 密钥算成实时的 6 位验证码。没有服务器、不用注册、没有数据库——每个"账号"就是一条**可收藏的网址**，密钥藏在它的 `#` 片段里（浏览器永远不会把 `#` 片段发到网络上）。

它的核心刻意做得极小、**无状态**：打开链接、看码、关掉，什么都不留。要同时盯很多账号？可选的**多号看板**会在本地留一份可一键清空的缓存，而你自己的链接列表文件才是底账。特别适合在不装 App、不做同步的情况下，管理**一大堆一次性 / 测试号的 2FA 密钥**。

## 用来做什么 / 什么场景用

- 🧮 **一屏看到多个号的码。** 把一个 `.txt` / `.md` / `.yaml` 的账号链接文件拖到页面上（或粘贴，或扫 **Google Authenticator 导出二维码**）→ 整屏彩色网格，所有码一次看全。你的文件始终是底账；看板只在本地缓存，随时可清空。
- 🗂️ **管理很多测试号。** 把每个号存成一条书签（`…/#secret=…&label=acme-test`）。点一下书签 → 立刻看到该号当前的验证码。不用装验证器 App、不用维护账号列表、不用同步。一个"测试号"书签文件夹就是你的总览面板。
- 🤝 **把账号临时借给同事 / 好友用一下。** 把该账号的链接发给对方，对方打开就能读到实时验证码登录——既不用把你的密码管理器凭据给他，也不用让他装验证器。*（链接里带着密钥，请通过可信渠道发送，用完及时轮换密钥。）*
- ⚡ **临时算一次码。** 在首页粘贴密钥、读出验证码、关掉标签页。天生无状态，什么都不存。
- 📱 **把密钥导到手机。** 点"显示二维码"，生成 `otpauth://` 二维码，直接扫进 Google Authenticator / Authy / 任意验证器 App。
- 🖥️ **把码投到大屏 / 录屏。** 给任意账号链接加 `&present=1`（或点"🖥 演示模式"），切换到放大的验证码、隐藏二维码和其它控件——投屏、共享屏幕或当面给人读时，密钥二维码绝不出现在屏幕上。*（只防你这边屏幕暴露——链接本身仍含密钥，不会对收到链接的人隐藏。）*

## 功能

- 🔐 **标准 TOTP**（RFC 6238），用 Web Crypto 本地计算——支持 SHA-1/256/512、可配位数与周期
- 🔗 **每个账号就是一条链接**——`#secret=…` 在 URL hash 里，绝不发往任何服务器
- 🧮 **多号看板**——拖入链接文件（或扫 Google Authenticator 迁移码）→ 彩色网格实时显示所有码；可导回 `.txt` 列表或一条看板链接
- 📷 **二维码导入**（扫一张二维码图）& **二维码导出**（`otpauth://`，方便导到手机）
- 🖥️ **演示模式**（`&present=1`）——只显示放大的滚动验证码、隐藏二维码等会引出密钥的 UI，便于投屏 / 录屏；对它"防什么、不防什么"如实说明
- 📲 **PWA**——可添加到主屏、完全离线可用
- 🚫 **无后端、无服务端存储、无埋点**——密钥永不接触服务器（看板的缓存仅在本地、可清空）
- 🪶 **小巧可移植**——纯静态文件 + 内置 React，哪里都能部署

## 截图

<p align="center">
  <img src="assets/screenshot-view.png" width="31%" alt="实时验证码" />
  <img src="assets/screenshot-setup.png" width="31%" alt="录入密钥" />
  <img src="assets/screenshot-qr.png" width="31%" alt="二维码导出" />
</p>
<p align="center"><sub>实时验证码与倒计时 · 粘贴密钥即出码 + 生成专属书签链接 · 导出为 <code>otpauth://</code> 二维码</sub></p>

## 部署说明

Easy2FA 是**纯静态**的——`index.html` + `support.js` + `vendor/`，**没有任何构建步骤**。唯一的硬性要求是 **HTTPS**（计算验证码的 Web Crypto API 需要安全上下文）；下面每个方案都自带 HTTPS（本地测试 `localhost` 也算，`file://` 不算）。

**所有平台最省心的路径都一样：先有一份属于你自己的副本，再连上它。**

> **为什么要先 fork？** fork 就是*你自己的*一份仓库拷贝。平台会盯着它，**每次 push 自动重新部署**，你可以随便改，而且全在免费额度内、零维护。上面的一键按钮其实已经替你 fork 了——每个按钮都会把本仓库 clone 进你的账号再部署那份拷贝。想自己动手 fork？先在 GitHub 点 **Fork**，再到下面任意平台用「导入 / 连接 Git」选你的 fork 即可。

仓库自带各平台配置（`wrangler.jsonc`、`netlify.toml`、`vercel.json`、`.nojekyll`），所以**没有任何构建项要填**——授权一下就能部署。

### GitHub Pages —— 免费，无需按钮

1. 把本仓库 **Fork** 到你的账号。
2. 你的 fork → **Settings** → **Pages**。
3. **Source：** 选 "Deploy from a branch" → **Branch：** `main` → **Folder：** `/ (root)` → **Save**。
4. 等约 1 分钟。站点地址为 `https://<你的用户名>.github.io/2FA/`（路径区分大小写，要和仓库名 `2FA` 一致）。

> 之所以在 `/2FA/` 子路径下也能跑，是因为应用全程用**相对路径**、Service Worker 作用域为 `./`。仓库自带一个空的 [`.nojekyll`](.nojekyll)，让 Pages 原样托管所有文件。

### Cloudflare

> Cloudflare 已于 2025 年对新项目停用 *Pages*——按钮现在创建的是一个**服务静态资源的 Worker**（结果一样：一个免费的 `*.workers.dev` HTTPS 地址）。

- **一键：** 顶部的 **Deploy to Cloudflare** 按钮（会把仓库 clone 进你的账号再部署），**或**
- **fork 后导入：** Cloudflare 控制台 → **Workers & Pages** → **Create** → **Import a repository** → 选你的 fork → 构建项全部留空 → **Deploy**。

仓库自带的 [`wrangler.jsonc`](wrangler.jsonc)（`assets.directory: "./"`）让它零配置——不用选框架、不用填构建命令、不用设输出目录。

### Netlify

- **一键：** 顶部的 **Deploy to Netlify** 按钮（clone + 部署），**或**
- **fork 后导入：** Netlify → **Add new project** → **Import an existing project** → **GitHub** → 授权 → 选你的 fork → **Build command** 和 **Publish directory** 都保持默认 → **Deploy**。

[`netlify.toml`](netlify.toml) 已固定 `publish = "."`（根目录）、无构建命令。*（按钮**或**手动 fork+导入，二选一，别两个都做，否则会有两份拷贝。）*

### Vercel

- **一键：** 顶部的 **Deploy with Vercel** 按钮（clone + 部署），**或**
- **fork 后导入：** Vercel → **Add New… → Project** → **Import Git Repository** → 选你的 fork → **Framework Preset：** `Other`，**Build** 与 **Output** 留空 → **Deploy**。

[`vercel.json`](vercel.json) 已固定 `Other` 预设（`"framework": null`）、无构建步骤、以仓库根目录为输出。

### 自己托管（任意静态服务器）

它就是一个静态文件夹，用什么托管都行，但要走 **HTTPS**：

```bash
# 本地开发（localhost 是安全上下文，验证码能正常算）
npx serve .
# 或
python3 -m http.server 8000
```

正式服务器请用 nginx / Caddy / Apache 并配 TLS 证书。纯 HTTP（非 localhost）或用 `file://` 直接打开 `index.html` **都算不出验证码**——Web Crypto 拒绝在非安全上下文运行。

> **更新已部署的版本：** 改动任何静态文件并重新部署后，记得提升 [`sw.js`](sw.js) 里的 `CACHE` 版本号（如 `2fa-v5` → `2fa-v6`），否则 Service Worker 会继续喂旧缓存。

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
- **多号看板**会把列表存进浏览器的 **localStorage**（仅本地缓存，应用本身绝不同步到任何地方）。点「清空」即可抹掉；你自己的文件才是真正的备份。
- **演示模式**（`&present=1`）只防**你这边屏幕**暴露（肩窥 / 共享屏幕 / 录屏）——靠隐藏二维码实现。它**不保护**收到链接的人：URL 里仍然带着密钥。它不是"安全分享"。

## 更新记录

- **2026-06** — **界面中英双语 + 一批硬化**：右上角一键切换中/EN（按浏览器语言 / `?lang` / localStorage 自动选择）。同批：拒绝 HOTP 链接和无效/截断书签链接的"算错码"路径、可访问性（键盘复制 / 减少动态 / 焦点环 / AA 对比度）、`qrcode.js` 延迟加载，新增 `tests.html`（RFC 6238 自测）与 `PATCHES.md`。
- **2026-06** — **零配置部署**：补齐 `wrangler.jsonc` / `netlify.toml` / `vercel.json` / `.nojekyll` 和 fork 优先的部署教程，到任意平台都是"授权 → 选你的 fork → 部署"，没有构建项要填。并更正了 Cloudflare 按钮（它现在创建的是带静态资源的 Worker，不是 Pages）。
- **2026-06** — **演示模式**（`&present=1`，可选 `&nolabel=1`）：放大的验证码 + 隐藏二维码和编辑控件，便于共享屏幕而不暴露密钥二维码。如实界定边界——它防的是你的屏幕，不是链接接收方。
- **2026-06** — **多号看板**：从列表文件 / 粘贴 / 剪贴板 / Google Authenticator 迁移码导入 → 彩色网格实时显示所有码（一个定时器驱动全部）。可导回 `.txt` 列表或一条 `#board=…` 链接。看板在本地留一份可一键清空的缓存——你的文件始终是底账。
- **2026-06** — 首个版本：无状态单账号视图、粘贴密钥即出码 + 专属书签链接、二维码导入 / 导出、PWA + 离线。

## 许可

[MIT](LICENSE)
