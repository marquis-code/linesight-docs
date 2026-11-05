# API quick reference

Base: `http://localhost:4110/api` · Swagger: `/api/docs`

## Public
| Method | Path | Purpose |
|---|---|---|
| GET | `/health` | Liveness |
| GET | `/public/stats` | Marketing counters |
| GET | `/catalog/products` | Product catalog (`?category`, `?q`) |
| GET | `/catalog/products/:slug` | Product detail |
| GET | `/plans` | Subscription plans |
| POST | `/configurator` | Pig sizing recommendation |
| POST | `/leads` | Contact/configurator lead |

## Auth
| Method | Path | Purpose |
|---|---|---|
| POST | `/auth/login` | `{email,password}` → JWT |
| GET | `/auth/me` | Current user/org |

## Portal (Bearer JWT)
| Method | Path | Purpose |
|---|---|---|
| GET | `/portal/overview` | Dashboard aggregate |
| GET/POST | `/portal/assets` | Asset register |
| GET/POST | `/portal/inspections` | Run list / create report |
| GET | `/portal/reports/:id` | Full report + asset |
| GET | `/portal/reports/:id/pdf` | Server-generated PDF |
| GET | `/portal/reports/:id/comparison` | Previous-run depth deltas |
| GET/POST | `/portal/orders` | Marketplace orders |
| GET/POST | `/portal/rentals` | Kit reservations |
| POST | `/portal/rentals/:id/pickup` | Mark picked up |
| POST | `/portal/rentals/:id/return` | Return, late fee, deposit refund |
| GET | `/portal/invoices` | Customer invoices |
| GET | `/portal/invoices/:id/pdf` | Invoice PDF |
| POST | `/portal/invoices/:id/pay` | Capture payment |
| GET/POST | `/portal/subscription` | Plan status / switch |
| GET/POST | `/portal/jobs` | Home jobs |
| GET | `/portal/jobs/:id` | Job + bids + escrow |
| POST | `/portal/jobs/:id/analyze` | Visual analysis |
| POST | `/portal/jobs/:id/bids` | Submit contractor bid |
| POST | `/portal/jobs/:id/award` | Award bid + fund escrow |
| POST | `/portal/escrow/:jobId/release` | Release milestone |
| GET | `/portal/contractor/opportunities` | Contractor queue |
| GET/POST | `/portal/contractor/profile` | Licence/insurance profile |
| GET | `/portal/contractor/work-orders` | Assigned work |
| POST | `/portal/work-orders/:id/complete` | Completion evidence |
| GET | `/portal/notifications` | Notification inbox |
| POST | `/portal/notifications/:id/read` | Mark read |
| GET | `/portal/partner/overview` | Manufacturer workspace |
| POST | `/portal/partner/products` | Publish catalog item |
| POST | `/portal/partner/products/:id` | Update listing |

## Admin (Bearer JWT, owner/admin/ops)
`/admin/overview`, `/admin/orgs`, `/admin/orders`, `/admin/rentals`,
`/admin/reports`, `/admin/jobs`, `/admin/products`, `/admin/plans`,
`/admin/subscriptions`, `/admin/manufacturers`, `/admin/leads`,
`/admin/invoices`, `/admin/work-orders`, `/admin/contractors`, `/admin/users`,
`/admin/audit`, `/admin/notifications`, `/admin/settings`.

Status writes:
- `POST /admin/orders/:id/status`
- `POST /admin/rentals/:id/status`
- `POST /admin/jobs/:id/status`
- `POST /admin/leads/:id/status`
- `POST /admin/products/:id`

## Engine
- `GET http://localhost:4210/health`
- `POST /v1/ili/analyze` — `{asset, rows, previous}`
- `POST /v1/video/analyze` — `{ref,address,issue,videoUrl}`
