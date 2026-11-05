# Feature coverage matrix

Legend: **Working** = implemented and tested locally; **Adapter** = implemented
against a local/provider adapter, needs production credentials; **Roadmap** =
intentionally not built in this repository.

## Accounts, tenancy and access
| Feature | Status | Where |
|---|---|---|
| Registration (operator, utility, homeowner, contractor, manufacturer) | Working | `/signup`, `POST /auth/register` |
| Login / logout / JWT session | Working | `/login`, `/auth/login` |
| Forgot / reset password | Working | `/forgot`, `/reset`, `/auth/forgot`, `/auth/reset` |
| Organisation scoping | Working | `orgId` on all portal entities |
| Roles: owner, admin, ops, engineer, contractor, homeowner, manufacturer | Working | JWT claims + admin user editor |
| User disable / role change | Working | Studio → Users |
| SSO / SCIM | Roadmap | needs enterprise IdP |

## B2B integrity
| Feature | Status | Where |
|---|---|---|
| Asset register | Working | `/app/assets` |
| CSV/file upload | Working | `/app/inspections`, multipart API |
| ILI ingestion (MFL/caliper fields) | Working | API + FastAPI engine |
| ASME B31G screening | Working | engine + report |
| CGR / remaining life | Working | engine + report |
| Ranked anomaly register | Working | `/app/reports/:id` |
| PDF evidence pack | Working | `/portal/reports/:id/pdf` |
| 3D anomaly twin | Working | Three.js `ReportTwin` |
| Multi-run comparison | Working | `/portal/reports/:id/comparison` |
| GIS route map | Roadmap | lat/lon stored; no tile provider |
| Vendor-specific parsers | Roadmap | generic CSV contract |

## Commerce
| Feature | Status | Where |
|---|---|---|
| Product catalog / search / detail | Working | `/marketplace` |
| Sizing configurator | Working | `/configurator` |
| Cart and order | Working | `/app/marketplace` |
| Inventory decrement | Working | `createOrder` |
| Invoice/payment ledger | Working | billing, Studio invoices |
| Refund | Working | Studio → Invoices |
| PDF invoice | Working | invoice PDF endpoints |
| Stripe/Paystack live capture | Adapter | simulator provider; credentials required |
| Carrier freight/tax service | Roadmap | flat tax; no carrier API |
| Manufacturer self-service | Working | `/app/partner` |
| Manufacturer subscription | Working | Studio → Partners |

## Rentals
| Feature | Status | Where |
|---|---|---|
| Catalog / weekly price | Working | `/rentals` |
| Availability overlap check | Working | `GET /portal/rentals/availability` |
| Deposit / invoice | Working | rental creation |
| Pickup | Working | portal |
| Return / late fee / deposit refund | Working | portal |
| Logistics carrier integration | Roadmap | no carrier API |

## B2C / LineSight Home
| Feature | Status | Where |
|---|---|---|
| Homeowner job creation | Working | `/app/home` |
| Footage reference / upload | Working (reference) | job video URL; object storage adapter pending |
| Visual findings / health score | Working (deterministic) | engine `/v1/video/analyze` |
| Health certificate | Working | job detail |
| Contractor bids | Working | job detail |
| Award / escrow funding | Working | job detail |
| Milestone release | Working | job detail |
| Real computer-vision model | Roadmap | needs labelled dataset/model |
| Regulated escrow | Adapter | local milestone ledger |

## Contractor operations
| Feature | Status | Where |
|---|---|---|
| Profile submission | Working | `/app/contractor` |
| Admin verification | Working | Studio → Contractors |
| Work order created on award | Working | API |
| Completion notes/photos | Working | contractor portal/API |
| Admin verification + escrow release | Working | Studio → Work orders |
| Scheduling calendar | Roadmap | scheduledAt field exists |
| License/insurance automated checks | Roadmap | manual review |

## Operations Studio
| Feature | Status |
|---|---|
| Revenue dashboard | Working |
| Customers | Working |
| Orders | Working |
| Invoices/refunds | Working |
| Rentals | Working |
| Reports | Working |
| Home jobs | Working |
| Work orders | Working |
| Catalog edit | Working |
| Subscriptions | Working |
| Partners/approval | Working |
| Contractors/verification | Working |
| Users/roles | Working |
| Leads | Working |
| Audit trail | Working |
| Notifications queue | Working |
| Runtime settings (tax, support) | Working |

## Platform
| Feature | Status |
|---|---|
| PWA manifest / offline shell | Working |
| Responsive web / portal | Working |
| Self-hosted fonts and imagery | Working |
| SMTP/SMS delivery | Adapter | outbox records; provider credentials needed |
| S3/R2 object storage | Adapter | local uploads |
| Webhooks/idempotency | Roadmap |
| Observability/OpenTelemetry | Roadmap |
