# Security, compliance and production gaps

## Implemented
- JWT bearer auth with role and org claims.
- Tenant scoping on portal reads/writes (`orgId` derived from the token).
- Password hashing with bcrypt (12 rounds).
- Admin routes protected by role guard (`owner`, `admin`, `ops`).
- CORS allow-list for the two frontends.
- Validation pipe and Mongoose schema typing.
- No secrets committed; `.env.example` only.
- Report calculations carry inputs and method in the stored record.
- Responsive, keyboard-accessible forms and focus states.

## Must be completed before production
1. **Payments** — replace simulated checkout/rental/escrow with Stripe/Paystack,
   webhooks, idempotency and reconciliation.
2. **File storage** — signed S3/R2 uploads, virus scanning, retention and
   tenant-scoped download URLs. Current demo accepts row data, not raw files.
3. **AI validation** — the visual engine is deterministic. A real model needs a
   labelled dataset, evaluation, confidence calibration and human review.
4. **PDF/report delivery** — server-rendered, signed PDF with a report hash and
   reviewer identity.
5. **SSO/SCIM** — enterprise operators require SAML/OIDC, SCIM provisioning and
   session revocation.
6. **Audit** — append-only audit store, admin action logging and export.
7. **Escrow** — operate through a licensed payments/escrow partner; do not hold
   customer funds directly.
8. **GIS/3D** — replace the canvas profile with map tiles and a true 3D/WebGL
   viewer if the sales motion requires it.
9. **Notifications** — transactional email/SMS, escalation and SLA timers.
10. **Compliance** — privacy policy, DPA, SOC 2 roadmap, penetration test and
    data residency commitments.
