# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

XPayLabs official developer documentation site, built on Mintlify (MDX + YAML frontmatter), Luma theme, bilingual (EN + ZH).

## Commands

```bash
npx mintlify dev     # Local preview (localhost:3000)
```

No `package.json`, no build step. Deployment is handled automatically by the Mintlify GitHub App on push to the default branch.

## Configuration

- `docs.json` — site config (navigation structure, theme, SEO, favicon, tab groups)
- Pages are MDX files with optional React component embedding
- Chinese translations mirror the structure under `zh/`

## Terminology (project-wide)

- **"collection"** / **"collection order"** — receiving funds (not "payment")
- **"payout"** — sending funds
- **"order"** — generic term for both collections and payouts
- **"merchant token"** — auth credential (HMAC-signed, not API Key or Bearer Token)
- **"deposit address"** — address where customers send funds (not "payment address")
- **"sweep"** / **"collection"** — hot-to-cold wallet settlement
- **"webhook callback"** — merchant-registered callback URL (not "webhook endpoint")
- **"scanner"** — blockchain indexing component

## Content Boundaries

- **In scope**: public REST API (v1/order/*, v1/symbol/*) and integration guides
- **Out of scope**: internal admin features, dashboard UI details, Java/Spring Boot internals
- **Audience**: merchant/integrator perspective — how to use the API, not how it works internally

## Writing Style

- Active voice, second person ("you")
- One idea per sentence
- Sentence case for headings
- Bold UI elements: Click **Settings**
- Code formatting for file names, commands, paths, and code references
