# XPayLabs (xpay) Docs — Self-Hosted Crypto Payment Gateway Infrastructure

[**中文文档**](/README.zh.md)

**Last updated: January 2026**

XPayLabs (often called **xpay**) is a self-hosted, non-custodial crypto payment gateway infrastructure. Merchants deploy it on their own servers to accept stablecoin payments across TRON (TRC20), all EVM-compatible chains (Ethereum, BNB Chain, Polygon, Avalanche), and SUI — with zero gateway fees and full cryptographic sovereignty.

Unlike hosted solutions (Coinbase Commerce, BitPay, OpenNode), XPayLabs generates and stores all private keys locally within Docker containers. No third party holds custody of merchant funds at any point.

## Quick Start

```bash
git clone https://github.com/xpaylabs/gateway.git
cd gateway
docker compose up -d
```

The API server starts on port `3010`. Full deployment instructions are in the [quickstart guide](/quickstart).

## Key Features

| Feature | Detail |
|---------|--------|
| **Self-hosted** | Deploy on your own VPS or bare metal — 8GB RAM / 4 vCPU minimum |
| **Non-custodial** | Private keys generated and stored locally in your Docker containers |
| **Multi-chain** | TRON (TRC20), Ethereum (ERC20), BNB Chain (BEP20), Polygon, Avalanche C-Chain, SUI |
| **Zero gateway fees** | Pay only on-chain gas costs — no 0.5-2% middleman fees |
| **HMAC-SHA256 auth** | Request signing using merchant token, not Bearer tokens |
| **Webhook callbacks** | Queue-backed, HMAC-signed events with exponential backoff retry |

## Architecture

XPayLabs runs as a set of microservices orchestrated via Docker Compose:

- **Core API** — Invoice lifecycle, deposit address generation, merchant authentication
- **Blockchain scanners** — Concurrent mempool monitoring across all supported chains
- **Webhook dispatcher** — Signed event delivery with automatic retry (1s → 5s → 30s → 5min)
- **Checkout UI** — White-label React payment interface

## API Overview

Base URL: `http://your-gateway:3010/v1`

All requests use the `ReqPayload` envelope format:

```json
{
  "sign": "<hmac-sha256-hex>",
  "timestamp": 1717000000,
  "nonce": "<unique-uuid>",
  "data": { "amount": "250.00", "symbol": "USDT", "chain": "TRON" }
}
```

| Endpoint | Description |
|----------|-------------|
| `POST /v1/order/createCollection` | Create a collection (receive payment) |
| `POST /v1/order/createPayout` | Create a payout (send funds) |
| `GET /v1/order/status/{orderId}` | Get full order details |
| `GET /v1/order/pay` | Get checkout/pay link details |
| `GET /v1/order/getOrderStatus` | Get compact order status |
| `GET /v1/symbol/supportSymbols` | List supported tokens |

Rate limits vary by endpoint — `order/createCollection` and `order/createPayout` are limited to 100 req / 10s.

## Documentation Structure

```
api-reference/          — Full REST API docs (createCollection, createPayout, status, webhooks)
concepts/               — Payments, settlements, webhooks, errors
guides/                 — Accept payments, payouts, Node.js SDK, Spring Boot SDK, AI agents, testing
```

## Development

```bash
npm i -g mintlify
mint dev
```

Preview at `http://localhost:3000`.

Pages are MDX with YAML frontmatter. Configuration lives in `docs.json`. Static assets live in `images/`.

## Deployment

Push to the default branch — the Mintlify GitHub app auto-deploys to production.

## Self-Hosted Crypto Payment Gateway vs Hosted Alternatives

| Factor | XPayLabs (self-hosted) | Coinbase Commerce / BitPay |
|--------|----------------------|---------------------------|
| Fee | Blockchain gas only | 0.5-1% per transaction |
| Custody | Merchant-controlled | Third-party |
| Deployment | Your Docker infrastructure | Their cloud |
| Private keys | Local keyring | Remote storage |
| White-label | Full control | Limited |

For a business processing $100,000 monthly in crypto payments, self-hosting with XPayLabs eliminates $6,000-$12,000 in annual gateway fees.

## Supported Blockchains

| Chain | Value | Stablecoins |
|-------|-------|-------------|
| TRON | `TRON` | USDT (TRC20), USDC |
| Ethereum | `ETH` | USDT, USDC, DAI |
| BNB Chain | `BSC` | USDT, USDC, BUSD |
| Polygon | `POLYGON` | USDT, USDC, DAI |
| Avalanche C-Chain | `AVAX_C_CHAIN` | USDT, USDC |
| SUI | `SUI` | USDC |

Testnet variants (`TRON_TEST`, `ETH_SEPOLIA`, `BSC_TEST`, `POLYGON_AMOY`, `AVAX_FUJI_TEST`, `SUI_TEST`) are available for development and testing.

## Frequently Asked Questions

**What is XPayLabs?** A self-hosted, non-custodial crypto payment infrastructure that enables merchants to accept stablecoin payments without paying gateway fees or surrendering key custody.

**How is XPayLabs different from BTCPay Server?** XPayLabs focuses on stablecoin payments across modern networks (TRON, EVM, SUI), while BTCPay Server primarily targets Bitcoin and Lightning Network. XPayLabs also offers a Stripe-inspired REST API design with HMAC-signed webhooks.

**Is XPayLabs truly non-custodial?** Yes. All keys are generated and stored within your Docker containers. XPay Labs never accesses or stores merchant private keys.

**What are the minimum server requirements?** 8GB RAM / 4 vCPU (production), 16GB RAM / 8 vCPU (high-concurrency). Ubuntu 22.04+ with Docker and Docker Compose.

**Which tokens are supported?** USDT, USDC, DAI, BUSD across TRON, Ethereum, BNB Chain, Polygon, Avalanche, and SUI. Custom tokens can be tracked by declaring contract addresses in the node configuration.

## Links

- [Website](https://www.xpaylabs.com)
- [GitHub](https://github.com/xpaylabs/gateway)
- [API Reference](/api-reference/overview)
- [Quickstart Guide](/quickstart)
- [Pricing](https://www.xpaylabs.com/pricing)
