# 3PD Astro Static Starter Kit

A starter kit for building display-only Astro micro-frontends that deploy as Drupal block modules inside the HUDX platform.

## What is this?

HUDX is a Drupal-based platform. Third-party developers (3PDs) extend it by building self-contained applications using one of the 3PD starter kits. Each app gets packaged as a Drupal module — no Drupal development experience required.

This starter is for apps that only need to **display content from Drupal**. It fetches data from Drupal's JSON:API at runtime — no backend, no database, no server to manage.

**When to use this starter:**
- You need to display content managed in Drupal (nodes, fields, media)
- Your app is read-only — no forms, no user submissions
- You want the simplest possible setup

**When to use a different starter:**
- You need forms or data persistence → use the [Astro Forms starter](https://github.com/sweimer/3PD---Astro-Forms-Starter-Kit)
- You need a full React SPA with state → use the [React starter](https://github.com/sweimer/3PD---React-Starter-Kit)

---

## How it works

| Environment | Frontend | Data source |
|---|---|---|
| Local dev | Astro at `localhost:4321` | Drupal JSON:API (remote, set in `.env`) |
| Deployed in Drupal | Astro bundle loaded as a Drupal library | Drupal JSON:API (same-origin) |

Your app fetches content from Drupal's JSON:API — in both local dev and production. No local data store needed.

---

## Prerequisites

- Node.js v20+
- npm
- The 3PD CLI — see [3pd-ide](https://github.com/sweimer/sandbox-glazed) for installation instructions
- Access to the shared HUDX Drupal environment (for content)

You do **not** need a local Drupal install, Lando, or Docker.

---

## Getting started

**1. Get the starter**

Your HUDX project lead will scaffold your app using the 3PD CLI. If you are cloning this repo directly to explore:

```bash
git clone https://github.com/sweimer/3PD---Astro-Static-Starter-Kit.git my-app
cd my-app
npm install
cp .env.example .env
```

**2. Set your Drupal URL**

Open `.env` and set the shared Drupal environment URL:

```
PUBLIC_DRUPAL_BASE_URL=https://dev-3-pd-ide.pantheonsite.io
```

**3. Start the dev server**

```bash
npm run dev
```

Opens Astro at `http://localhost:4321`. Your app fetches content live from Drupal.

Edit files in `src/` — hot reload is active.

---

## Project structure

```
your-app/
├── src/
│   ├── layouts/
│   │   └── Layout.astro    ← HTML shell — edit page structure here
│   └── pages/
│       └── index.astro     ← Start here — your app UI + JSON:API fetch
├── .env                    ← Local config (gitignored)
├── .env.example            ← Commit this — no secrets
└── package.json
```

**Where to start building:** `src/pages/index.astro`. Replace the placeholder fetch URL with your content type's JSON:API endpoint.

---

## Fetching Drupal content

```js
// In src/pages/index.astro
const DRUPAL_BASE = import.meta.env.PUBLIC_DRUPAL_BASE_URL;
const res = await fetch(`${DRUPAL_BASE}/jsonapi/node/YOUR_CONTENT_TYPE`);
const json = await res.json();
const nodes = json.data;
```

Replace `YOUR_CONTENT_TYPE` with the machine name of your Drupal content type (e.g. `basic_page`, `article`).

> **Note:** Do not append query string parameters with square brackets (e.g. `?fields[node--page]=title`). Some proxy configurations strip bracket notation, returning empty results with no error. Use the plain base URL and filter in JavaScript if needed.

---

## Packaging for Drupal

When your feature is ready to hand off:

```bash
3pd astro module
```

This builds the Astro app and generates a Drupal module folder in your app directory. Commit that folder and push your feature branch. The HUDX team handles installation.

> **Do not run** `3pd astro module --install` — that flag requires internal Drupal access and is for HUDX team use only.

---

## Important gotchas

- **Content lives in Drupal.** To update what your app displays, update the content in Drupal — no code deployment needed.
- **App structure changes require a redeploy.** If you change `src/` files, you need to run `3pd astro module` and push again.
- **`PUBLIC_` prefix is required** for any env var you need in the Astro frontend (e.g. `PUBLIC_DRUPAL_BASE_URL`).
