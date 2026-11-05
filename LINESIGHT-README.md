# LineSight — Pipeline Diagnostics & Pigging Platform

A working, full-stack implementation of the commercial platform described in the
*Next-Generation Pipeline Diagnostics & Pigging Commercial Platform* proposal.

LineSight connects inspection and pigging data to the commercial workflows around
it: a vendor-agnostic integrity workspace for B2B operators and utilities, a
self-service marketplace and rental hub, a camera-first homeowner product, a
verified contractor network, and milestone escrow.

![LineSight homepage](docs/screenshots/home.png)

## Repositories

The platform is split into deployable repositories:

| Repository | Responsibility |
|---|---|
| [`linesight-web`](https://github.com/marquis-code/linesight-web) | Nuxt 3 public website + B2B/B2C customer portal |
| [`linesight-studio`](https://github.com/marquis-code/linesight-studio) | Nuxt 3 operations console (SPA) |
| [`linesight-api`](https://github.com/marquis-code/linesight-api) | NestJS 11 + MongoDB core API |
| [`linesight-engine`](https://github.com/marquis-code/linesight-engine) | FastAPI diagnostic engine (B31G, CGR, visual triage) |
| [`linesight-docs`](https://github.com/marquis-code/linesight-docs) | Proposal, architecture, revenue model and runbooks |

This workspace is the integration monorepo used to run them together.

## Run locally

Prerequisites: Node 22+, pnpm 10+, Docker, Python 3.12+.

```bash
# 1. Mongo (port 27019, no conflict with other local projects)
docker compose up -d mongo

# 2. API — seeds automatically on first boot
pnpm install
pnpm --filter @linesight/api build
pnpm --filter @linesight/api start

# 3. Diagnostic engine
cd services/engine
python3 -m venv .venv
.venv/bin/pip install -r requirements.txt
.venv/bin/uvicorn main:app --port 4210

# 4. Web (port 3300)
pnpm --filter @linesight/web dev

# 5. Studio (port 3210)
pnpm --filter @linesight/admin dev
```

| Surface | URL |
|---|---|
| Website / customer portal | http://localhost:3300 |
| Studio (operations) | http://localhost:3210 |
| API | http://localhost:4110/api |
| Swagger | http://localhost:4110/api/docs |
| Engine | http://localhost:4210/health |

Port 3300 is used because 3000/3200 are occupied by unrelated local services.

## Demo accounts

All customer accounts use `LineSightDemo!2026`.

| Role | Email |
|---|---|
| Operator owner (Northstar Midstream) | `demo@linesight.io` |
| Utility engineer (Harris County Water) | `utility@linesight.io` |
| Homeowner (Cedar Ridge HOA) | `homeowner@linesight.io` |
| Contractor (Precision Trenchless) | `contractor@linesight.io` |
| Manufacturer (PolyForm Supply) | `supplier@linesight.io` |
| Studio operations admin | `ops@linesight.io` / `LineSightOps!2026` |

The seed includes two pipeline operators, live B31G reports, uploaded-run history,
a homeowner job with three findings and contractor bids, marketplace products,
orders, invoices, payments, rentals, subscriptions, a verified contractor profile,
work orders, manufacturers, notifications, audit events and inbound leads.

## What works end to end

- **B2B diagnostic workflow** — upload CSV rows or run the demo dataset against
  a seeded asset; the API calls the engine (or a deterministic fallback), stores
  an ASME B31G/CGR report, and renders the anomaly profile and register.
- **Marketplace** — catalog filtering, product detail, cart and real order
  creation with an 8% commission ledger in Studio.
- **Rentals** — kit reservation with dates, weekly pricing, deposit and status
  management in Studio.
- **Subscriptions** — Foundation/Scale/Enterprise plans, mileage-based annual
  pricing and plan switching.
- **LineSight Home** — homeowner job creation, visual analysis, health
  certificate, contractor bids, award and LineAssure escrow release.
- **Contractor network** — scoped opportunities, bid submission and award.
- **Studio** — revenue overview, customers, orders, rentals, reports, jobs,
  catalog editing, subscriptions, partners and leads.
- **SEO/performance** — SSR marketing pages, route-level SPA for authenticated
  portal routes, self-hosted fonts, responsive AVIF/WebP images.

## Full workflow coverage

- **File ingestion** — multipart CSV upload, server-side parsing, stored file path
  and anomaly rows, automatic previous-run comparison.
- **Report delivery** — server-generated PDF evidence pack and downloadable
  invoice PDFs.
- **Digital twin** — interactive Three.js anomaly orbit view with canvas fallback
  for devices without WebGL.
- **Payments and refunds** — invoice issue, capture, payment ledger, refunds and
  customer/admin PDFs. Provider mode is selected with `PAYMENTS_PROVIDER`.
- **Rental lifecycle** — availability check, reservation, pickup, return, late
  fee, deposit refund and invoice.
- **Contractor operations** — profile/licence/insurance submission, admin
  verification, awarded bid → work order → completion evidence → admin
  verification → escrow release.
- **Manufacturer self-service** — partner workspace with catalog creation,
  publish/unpublish and product inventory.
- **Customer communications** — notification inbox, read state and Studio
  notification queue.
- **Governance** — audit trail for orders, rentals, invoices, contractors,
  reports, settings and user changes; user role/active-state administration.
- **Accounts** — self-service registration, login, forgot/reset password and
  logout.
- **PWA** — installable manifest, offline shell and cached imagery.

## Explicit production gaps

The application is fully functional locally with seeded data and local provider
adapters. Before real customers or money move, external accounts are still
required for:

- Stripe/Paystack credentials and webhook secrets (a complete invoice/payment/refund ledger runs locally in simulator mode).
- S3/R2 for multi-region file storage (local uploads and PDF generation are working).
- Production ML model for video triage (the contract and deterministic engine are working).
- SMTP/SMS delivery, e-signature and notarisation (the outbox, notifications and signed PDFs are built).
- SSO/SCIM, immutable audit sink and penetration testing.
- Real manufacturer contracts and carrier rates (inventory, tax and invoices are built).
- GIS tiles for the route map (the interactive Three.js digital twin and canvas fallback are built).

See [`docs/architecture.md`](docs/architecture.md), [`docs/revenue-model.md`](docs/revenue-model.md)
and [`docs/security-and-gaps.md`](docs/security-and-gaps.md).

## Source proposal

[`docs/Pipeline_Diagnostics_Platform_Proposal.pdf`](docs/Pipeline_Diagnostics_Platform_Proposal.pdf)
