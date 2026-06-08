# XPayLabs（xpay）文档 — 自托管加密支付网关基础设施

[**English**](/README.md)

**最后更新：2026 年 1 月**

XPayLabs（常称 **xpay**）是一款自托管、非托管的加密支付网关基础设施。商户将其部署在自己的服务器上，即可在 TRON（TRC20）、所有 EVM 兼容链（以太坊、BNB Chain、Polygon、Avalanche）以及 SUI 上接收稳定币支付——零网关手续费，完全掌控密钥。

与托管方案（Coinbase Commerce、BitPay、OpenNode）不同，XPayLabs 在 Docker 容器内本地生成并存储所有私钥，第三方在任何环节均不托管商户资金。

## 快速开始

```bash
git clone https://github.com/xpaylabs/gateway.git
cd gateway
docker compose up -d
```

API 服务器启动在 `3010` 端口。完整部署说明请参阅[快速入门指南](/quickstart)。

## 核心功能

| 功能 | 说明 |
|---------|--------|
| **自托管** | 部署在您自己的 VPS 或裸金属服务器上 — 最低 8GB 内存 / 4 vCPU |
| **非托管** | 私钥在您的 Docker 容器内本地生成并存储 |
| **多链支持** | TRON（TRC20）、以太坊（ERC20）、BNB Chain（BEP20）、Polygon、Avalanche C-Chain、SUI |
| **零网关手续费** | 仅需支付链上 Gas 费用 — 无 0.5-2% 中间商费用 |
| **HMAC-SHA256 认证** | 使用商户令牌签名请求，而非 Bearer 令牌 |
| **Webhook 回调** | 队列驱动的 HMAC 签名事件，支持指数退避重试 |
| **x402 协议** | 面向 AI 代理和 LLM API 的按调用 USDC 微支付——买家 SDK（`@xpaylabs/x402`）+ 卖家中间件（`@x402/express`） |

## 架构

XPayLabs 以一组通过 Docker Compose 编排的微服务运行：

- **Core API** — 账单生命周期、充值地址生成、商户认证
- **区块链扫描器** — 跨所有支持链的并发内存池监控
- **Webhook 调度器** — 签名事件投递，自动重试（1 秒 → 5 秒 → 30 秒 → 5 分钟）
- **结账 UI** — 白标 React 支付界面

## API 概览

基础 URL：`http://your-gateway:3010/v1`

所有请求使用 `ReqPayload` 信封格式：

```json
{
  "sign": "<hmac-sha256-hex>",
  "timestamp": 1717000000,
  "nonce": "<unique-uuid>",
  "data": { "amount": "250.00", "symbol": "USDT", "chain": "TRON" }
}
```

| 端点 | 说明 |
|----------|-------------|
| `POST /v1/order/createCollection` | 创建收款订单 |
| `POST /v1/order/createPayout` | 创建付款订单 |
| `GET /v1/order/status/{orderId}` | 获取完整订单详情 |
| `GET /v1/order/pay` | 获取结账/支付链接详情 |
| `GET /v1/order/getOrderStatus` | 获取精简订单状态 |
| `GET /v1/symbol/supportSymbols` | 查看支持代币列表 |

各端点有不同速率限制——`order/createCollection` 和 `order/createPayout` 限制为 100 次请求 / 10 秒。

## 文档结构

```
api-reference/          — 完整 REST API 文档（createCollection、createPayout、状态查询、webhook）
concepts/               — 支付、结算、webhook、错误处理
guides/                 — 接收付款、付款、Node.js SDK、Java SDK（Spring Boot）、AI 代理、测试、x402 协议
```

## 本地开发

```bash
npm i -g mintlify
mint dev
```

预览地址：`http://localhost:3000`

页面为带 YAML 前置元数据的 MDX 文件。配置信息在 `docs.json` 中。静态资源位于 `images/` 目录下。

## 部署

推送到主分支——Mintlify GitHub 应用会自动部署到生产环境。

## 自托管加密支付网关 vs 托管方案对比

| 对比维度 | XPayLabs（自托管） | Coinbase Commerce / BitPay |
|--------|----------------------|---------------------------|
| 费用 | 仅链上 Gas 费 | 每笔交易 0.5-1% |
| 托管方式 | 商户自主控制 | 第三方托管 |
| 部署 | 您的 Docker 基础设施 | 它们的云端 |
| 私钥 | 本地密钥环 | 远程存储 |
| 白标 | 完全自定义 | 有限制 |

对于每月处理 $100,000 加密支付的业务，使用 XPayLabs 自托管方案每年可节省 $6,000-$12,000 网关费用。

## 支持的区块链

| 链 | 标识 | 稳定币 |
|-------|-------|-------------|
| TRON | `TRON` | USDT（TRC20）、USDC |
| Ethereum | `ETH` | USDT、USDC、DAI |
| BNB Chain | `BSC` | USDT、USDC、BUSD |
| Polygon | `POLYGON` | USDT、USDC、DAI |
| Avalanche C-Chain | `AVAX_C_CHAIN` | USDT、USDC |
| SUI | `SUI` | USDC |

测试网络（`TRON_TEST`、`ETH_SEPOLIA`、`BSC_TEST`、`POLYGON_AMOY`、`AVAX_FUJI_TEST`、`SUI_TEST`）可用于开发和测试。

## 常见问题

**什么是 XPayLabs？** 一款自托管、非托管的加密支付基础设施，商户可借此接收稳定币支付，无需支付网关手续费或交出密钥控制权。

**XPayLabs 与 BTCPay Server 有何不同？** XPayLabs 专注于现代网络（TRON、EVM、SUI）上的稳定币支付，而 BTCPay Server 主要面向比特币和闪电网络。XPayLabs 还提供受 Stripe 启发的 REST API 设计及 HMAC 签名 webhook。

**XPayLabs 真的是非托管吗？** 是的。所有密钥均在您的 Docker 容器内生成并存储，XPayLabs 从不访问或存储商户私钥。

**最低服务器要求是什么？** 8GB 内存 / 4 vCPU（生产环境），16GB 内存 / 8 vCPU（高并发）。Ubuntu 22.04+，需安装 Docker 和 Docker Compose。

**支持哪些代币？** 支持 TRON、以太坊、BNB Chain、Polygon、Avalanche 和 SUI 上的 USDT、USDC、DAI、BUSD。可通过在节点配置中声明合约地址来自定义追踪其他代币。

## 链接

- [官方网站](https://www.xpaylabs.com)
- [GitHub 仓库](https://github.com/xpaylabs/gateway)
- [API 参考](/api-reference/overview)
- [快速入门指南](/quickstart)
- [x402 协议 — AI 代理 USDC 微支付](/guides/x402-overview)
- [定价方案](https://www.xpaylabs.com/zh/pricing)
