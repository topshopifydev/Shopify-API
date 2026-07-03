<div align="center">

# 🛍️ Shopify Live Sales Counter

**A real‑time Shopify sales dashboard that runs on serverless — zero database, instant deploy.**

Track your store's revenue as it happens, with an animated counter and a monthly‑goal progress bar you can edit right in the browser.

[![Deploy with Vercel](https://img.shields.io/badge/Deploy-Vercel-000000?logo=vercel&logoColor=white)](https://vercel.com/new)
![Node](https://img.shields.io/badge/Node-%3E%3D18-339933?logo=node.js&logoColor=white)
![TypeScript](https://img.shields.io/badge/TypeScript-5.x-3178C6?logo=typescript&logoColor=white)
![Shopify Admin API](https://img.shields.io/badge/Shopify-Admin_GraphQL-96BF48?logo=shopify&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-blue)

</div>

---

## ✨ Features

- **⚡ Live counter** — the page polls `/api/sales` every 5 seconds and animates the number toward the latest total.
- **🎯 Monthly goals** — set a target per month with a slider or inline‑editable value; a color‑changing progress bar shows how close you are.
- **🔗 Shopify Admin GraphQL API** — aggregates real order totals (revenue, tax, discounts, shipping) with automatic cursor pagination over up to 250 orders per page.
- **🏬 Multi‑store ready** — whitelist multiple shops via `ALLOWED_SHOPS`, each with its own token, plus a single‑shop fallback for simple setups.
- **📅 Custom date ranges** — pass `from` / `to` ISO timestamps to count orders within any window.
- **🔒 Optional API key** — lock down the API with an `x-api-key` header.
- **🩺 Health endpoint** — `/api/health` reports the active API version and which shops have valid tokens.
- **🗄️ No backend state** — goals and settings persist in `localStorage`; the app is fully static + serverless.

## 🏗️ How it works

```
Browser (index.html)
   │  fetch /api/sales?from=…&to=…  every 5s
   ▼
Vercel Serverless Function (api/sales.ts)
   │  X-Shopify-Access-Token
   ▼
Shopify Admin GraphQL API  →  orders(query: "created_at:>=… status:any")
   │  paginate via cursor, sum totals
   ▼
{ ok: true, total: { count, order_totals, tax, discounts, shipping } }
```

## 🚀 Quick start

```bash
# 1. Install (includes the Vercel CLI for local dev)
npm install          # Node 18+

# 2. Configure your store
cp .env.example .env
```

Set these environment variables (locally in `.env`, and in your Vercel project for deploys):

| Variable | Required | Description |
| --- | --- | --- |
| `SHOPIFY_SHOP` | ✅ | Your `*.myshopify.com` domain |
| `SHOPIFY_TOKEN` | ✅ | Admin API token with the `read_orders` scope |
| `SHOPIFY_API_VERSION` | — | Admin API version (default `2025-07`) |
| `ALLOWED_SHOPS` | — | Comma‑separated shop domains for multi‑store mode |
| `SHOPIFY_TOKEN__<SHOP>` | — | Per‑shop token in multi‑store mode |
| `API_KEY` | — | If set, requests must send it in the `x-api-key` header |

```bash
# 3. Run it
vercel dev
# open index.html → watch the counter climb
```

## 📡 API

### `GET /api/sales`

Aggregates order totals for a shop and date range.

| Query param | Description |
| --- | --- |
| `from` | ISO 8601 start timestamp (defaults to start of month) |
| `to` | ISO 8601 end timestamp (defaults to end of month) |
| `shop` | Shop domain (defaults to `SHOPIFY_SHOP`) |

```bash
curl 'https://your-app.vercel.app/api/sales?from=2024-04-01T00:00:00Z&to=2024-04-30T23:59:59Z'
```

```json
{
  "ok": true,
  "perShop": [{ "shop": "9rds.myshopify.com", "totals": { "count": 42, "order_totals": 123.45, "tax": 1.23, "discounts": 0, "shipping": 4.56 } }],
  "total": { "count": 42, "order_totals": 123.45, "tax": 1.23, "discounts": 0, "shipping": 4.56 },
  "number": 123.45
}
```

### `GET /api/health`

Returns the active API version and token status per allowed shop.

## 🧪 Testing & type‑checking

```bash
npm test        # node --test
npm run typecheck
```

## 🛠️ Tech stack

TypeScript · Vercel Serverless Functions · Shopify Admin GraphQL API · Vanilla JS/HTML frontend · `localStorage` persistence

## 🐛 Troubleshooting

- In Vercel, define `SHOPIFY_SHOP`, `SHOPIFY_TOKEN` (and optionally `SHOPIFY_API_VERSION`) for **both** Production and Preview, then redeploy.
- Check **Runtime Logs** for `Shopify HTTP error` or `Invalid JSON from Shopify`.
- Verify your token directly:
  ```bash
  curl -H "X-Shopify-Access-Token: <TOKEN>" \
    https://<SHOP>.myshopify.com/admin/api/2025-07/orders/count.json
  ```
  A healthy response looks like `{ "count": n }`. If not, regenerate the token.

## 🤝 Contributing

Issues and pull requests are welcome. If this project helps you, a ⭐ goes a long way!

## 📄 License

MIT — free to use, modify, and share.
