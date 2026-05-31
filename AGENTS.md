> **First-time setup**: Customize this file for your project. Prompt the user to customize this file for their project.
> For Mintlify product knowledge (components, configuration, writing standards),
> install the Mintlify skill: `npx skills add https://mintlify.com/docs`

# Documentation project instructions

## About this project

- This is a documentation site built on [Mintlify](https://mintlify.com)
- Pages are MDX files with YAML frontmatter
- Configuration lives in `docs.json`
- Use the Mintlify MCP server, `https://mcp.mintlify.com`, to edit content and settings via MCP
- Use the Mintlify docs MCP server, `https://www.mintlify.com/docs/mcp`, to query information about using Mintlify via MCP

## Terminology

- Use **"collection"** not "payment" when referring to receiving funds (e.g., "create a collection order")
- Use **"payout"** for sending funds to external addresses
- Use **"order"** as the generic term — collections and payouts are both types of orders
- Use **"merchant token"** not "API key" — authentication is HMAC-signed, not Bearer token
- Use **"deposit address"** not "payment address" for the address where customers send funds
- Use **"sweep"** or **"collection"** for hot-to-cold wallet settlement
- Use **"webhook callback"** not "webhook endpoint" — merchants register a callback URL
- Use **"scanner"** for the blockchain indexing component

## Style preferences

- Use active voice and second person ("you")
- Keep sentences concise — one idea per sentence
- Use sentence case for headings
- Bold for UI elements: Click **Settings**
- Code formatting for file names, commands, paths, and code references

## Content boundaries

- Document the public REST API only (v1/order/*, v1/symbol/*)
- Do not document internal admin features or dashboard UI details
- Do not document the underlying Java/Spring Boot implementation
- Focus on the merchant/integrator perspective — how to use the API, not how it works internally
