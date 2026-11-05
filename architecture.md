# LineSight architecture

## System context

```
                    ┌──────────────────────────────┐
 B2B operator ─────▶│  linesight-web (Nuxt 3 SSR)  │
 B2C homeowner ────▶│  marketing + customer portal │
 Contractor ───────▶└──────────────┬───────────────┘
                                   │ HTTPS / JSON
                    ┌──────────────▼───────────────┐
                    │  linesight-api (NestJS 11)   │
                    │ auth · tenancy · assets      │
                    │ reports · market · rentals   │
                    │ jobs · bids · escrow         │
                    └───────┬──────────────┬───────┘
                            │              │
                  MongoDB 7 │              │ HTTP
                            │      ┌───────▼────────────────┐
                            │      │ linesight-engine       │
                            │      │ FastAPI · B31G · CGR   │
                            │      │ visual triage contract │
                            │      └────────────────────────┘
                            │
              ┌─────────────▼──────────────┐
              │  linesight-studio (Nuxt)   │
              │  operations / revenue      │
              └────────────────────────────┘
```

## Services

### `linesight-web` — Nuxt 3.21
- SSR marketing routes for SEO.
- `/app/**` is client-rendered because the session lives in a bearer token.
- Marketing: home, intelligence, homeowners, marketplace, product detail,
  configurator, rentals, pricing, contact, contractors, security, insights.
- Portal: overview, assets, inspections, reports, marketplace/cart, orders,
  rentals, home jobs, contractor bids, billing.
- Self-hosted Clash Display/Satoshi/IBM Plex Mono; responsive AVIF/WebP/JPEG
  images; canvas anomaly profile.

### `linesight-studio` — Nuxt 3 SPA
- Staff-only operations console on port 3210.
- Talks only to `/api/admin/*` with a JWT.
- Revenue dashboard, customers, orders, rentals, reports, B2C jobs, catalog,
  subscriptions, manufacturer partners, leads and runtime settings.

### `linesight-api` — NestJS 11
- MongoDB via Mongoose. Database `linesight` on `localhost:27019`.
- JWT bearer auth (7 days in the demo); roles `owner`, `admin`, `engineer`,
  `contractor`, `homeowner`, `ops`.
- Every portal entity carries `orgId`; controllers derive the org from the JWT
  rather than trusting request data.
- `EngineService` calls the Python engine with a 5-second timeout and falls back
  to a deterministic local implementation, so a demo never fails because the
  compute service is down.
- Swagger at `/api/docs`.

### `linesight-engine` — FastAPI
- `POST /v1/ili/analyze`: anomaly rows + asset metadata → B31G screening,
  corrosion growth rate, remaining life, heatmap points and safe pressure.
- `POST /v1/video/analyze`: deterministic visual findings behind a stable
  contract. Production ML replaces this function, not the API.
- No database. Stateless compute only.

## Core data model

| Collection | Purpose |
|---|---|
| `organizations` | Operator/utility/homeowner/contractor/manufacturer tenant |
| `users` | Auth + role + org membership |
| `assets` | Line size, wall, MAOP, product, install year, route |
| `inspectionruns` | An uploaded run, tool type, status and report link |
| `reports` | B31G results, CGR, remaining life, anomalies, heatmap |
| `products` | Marketplace/rental catalog, pricing, vendor, stock |
| `orders` | Marketplace checkout + 8% commission |
| `rentals` | Kit window, deposit, weekly subtotal, status |
| `plans` / `subscriptions` | Mileage-tier annual integrity plans |
| `manufacturers` | Supply-side partner subscriptions and listing counts |
| `jobs` | Homeowner camera job, findings, certificate, status |
| `bids` | Contractor price/timeline/note on a scoped job |
| `escrows` | Funded amount + milestone release state |
| `leads` | Contact, partner and configurator demand |
| `invoices` / `payments` / `refunds` | Billing ledger and provider references |
| `contractorprofiles` | Licence, insurance, service areas and verification |
| `workorders` | Awarded bid, schedule, completion evidence and verification |
| `auditlogs` / `notifications` | Governance trail and communication outbox |
| `settings` | Runtime configuration such as tax and support routing |
| `passwordresets` | One-hour password recovery tokens |

## End-to-end flows

### ILI report
1. Web uploads metadata/CSV rows to `POST /api/portal/inspections`.
2. API verifies the asset belongs to the user's org.
3. `EngineService` posts to the FastAPI engine; failures fall back locally.
4. API stores the report, run and updated asset health in Mongo.
5. Web routes to `/app/reports/:id`; Studio sees it under Reports.

### Marketplace
1. Product catalog served publicly from `/api/catalog/products`.
2. Customer builds a cart in the portal.
3. `POST /api/portal/orders` snapshots item prices, computes commission,
   stores the order.
4. Studio updates fulfilment status through `POST /api/admin/orders/:id/status`.

### Home repair + escrow
1. Homeowner creates a job and references footage.
2. `POST /api/portal/jobs/:id/analyze` calls the visual engine, stores findings
   and moves the job to `bidding`.
3. Contractors submit bids.
4. Homeowner awards a bid; API creates a funded escrow with three milestones.
5. Milestones release through `POST /api/portal/escrow/:jobId/release`.

## Additional implemented flows

### Upload and PDF
`POST /api/portal/inspections/upload` accepts a multipart CSV, stores it under
`UPLOAD_DIR`, parses rows server-side, screens them and stores the report. The
report/invoice PDFs are generated on demand by `PdfService` and streamed as
`application/pdf`.

### Digital twin and comparison
The report page renders a Three.js pipeline with anomaly rings and depth
markers. `GET /api/portal/reports/:id/comparison` aligns the previous report on
the same asset and returns per-anomaly depth deltas.

### Money
Orders, rentals and subscriptions issue an invoice and capture a payment in the
same transaction-like workflow. Admin refunds write a `refunds` record and move
the invoice to `refunded`. The provider adapter is selected by
`PAYMENTS_PROVIDER`; simulator mode is the default.

### Contractor verification
Awarding a bid creates a `workorder`. The contractor submits completion notes
and photo URLs, then Studio verifies the work. Verification releases all escrow
milestones and increments the contractor's completed-job count.

## Production topology
- Web + Studio on a Node host/CDN; API and Engine on private container services.
- MongoDB Atlas or a managed replica set.
- Object storage + signed uploads for raw runs and video.
- Queue (SQS/Redis) for long-running ingestion and PDF generation.
- Stripe/Paystack webhooks for marketplace/rental/subscription payments.
- OpenTelemetry, structured logs and an immutable audit sink.

## Why the split
The repositories are split by deployment boundary and failure domain:

- **Web** scales with public traffic and ships marketing copy independently.
- **Studio** is internal and can be network-restricted.
- **API** owns tenancy, money and state.
- **Engine** is compute-heavy and can be replaced/scaled independently.
- **Docs** carries the proposal, decisions and runbooks.

The integration monorepo is for local development only; each app has its own
lockfile-compatible package manifest and can be deployed alone.
