# LineSight — Pipeline Diagnostics & Pigging Platform

A working, full-stack MVP of the commercial platform described in the
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
| Studio operations admin | `ops@linesight.io` / `LineSightOps!2026` |

The seed includes two pipeline operators, a live B31G report, a homeowner job
with three findings and contractor bids, marketplace products, orders, rentals,
subscription plans, manufacturers and inbound leads.

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

## Explicit production gaps

This is a high-fidelity MVP, not a three-month production launch. Before real
customers or money move, the following need provider-backed implementation:

- Stripe/Paystack checkout and webhooks (payments are currently simulated).
- S3/R2 uploads for ILI files, videos and report PDFs (footage is referenced,
  not stored).
- Production ML model for video triage (the current engine is deterministic).
- Email/SMS notifications, signed PDF reports and e-signature.
- SSO/SCIM, immutable audit sink and penetration testing.
- Real manufacturer contracts, inventory feeds and tax/freight calculation.
- GIS/map tiles and true 3D/WebGL rendering (the report currently uses a
  performant canvas profile).

See [`docs/architecture.md`](docs/architecture.md), [`docs/revenue-model.md`](docs/revenue-model.md)
and [`docs/security-and-gaps.md`](docs/security-and-gaps.md).

## Source proposal

[`docs/Pipeline_Diagnostics_Platform_Proposal.pdf`](docs/Pipeline_Diagnostics_Platform_Proposal.pdf)
