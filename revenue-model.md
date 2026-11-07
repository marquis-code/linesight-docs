# Revenue model

The proposal calls for nine revenue streams across B2B and B2C. The platform
implements the product surfaces and ledger fields for the core streams.

## B2B

| Stream | Product surface | Current behavior | Production step |
|---|---|---|---|
| Integrity subscriptions | Foundation / Scale / Enterprise plans | Mileage-based annual value; plan switching | Stripe subscription + metering |
| Marketplace take rate | Vomtek Market | Orders with 8% commission recorded | Payment capture, tax, freight, returns |
| Rentals / HaaS | Rentals catalog | Weekly price, deposit, reservation, Studio status | Availability calendar + payment hold |
| Manufacturer subscriptions | Studio → Partners | Plan, monthly fee, listing count | Self-serve partner portal + billing |
| Data / API / enterprise | Intelligence positioning | Tenant-scoped API, Swagger, export-ready reports | Paid API keys, usage metering, SSO |

## B2C

| Stream | Product surface | Current behavior | Production step |
|---|---|---|---|
| Home line reports | Vomtek Home | $149 product, 24h positioning, report certificate | Payment + PDF delivery |
| Camera / kit rentals | Rentals | Camera kit reservation, refundable deposit | Logistics + deposit capture |
| Contractor referrals | Contractor network | Bids on scoped jobs | License verification + take rate |
| Vomtek Assure escrow | Home job detail | Funded milestones, release action | Regulated escrow/payment partner |
| Certification | Health certificate | Visual certificate stored on job | Standards body / verifiable credential |

## Marketplace economics
The API snapshots item price at checkout and stores:
- `subtotal`
- `commission` = 8% of subtotal
- `total`

Studio's overview sums:
- marketplace GMV (orders)
- annual subscription value (active subscriptions)
- rental reservations
- a separate "marketplace take" tile.

## Unit economics to validate with the client
- Cost per ILI report / analyst review.
- Rental utilisation and breakage.
- Contractor acquisition and bid win rate.
- Homeowner report conversion to repair.
- Manufacturer churn and listing quality.
