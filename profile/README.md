# Cuencor · Finaxis ERP

**Finaxis ERP** is a modular, multi-tenant SaaS platform built for small and mid-size businesses in Latin America. Each module is an independently deployable service that plugs into the core platform — teams only pay for what they use.

---

## Architecture

```
┌─────────────┐    ┌───────────────────────────────────────────────────┐
│  Angular 19  │    │                  NestJS Services                  │
│  Shell + MFE │◄──►│  core · accounting · real-estate · crm · loans   │
│  (Native Fed)│    └───────────────────┬───────────────────────────────┘
└─────────────┘                        │
                          ┌────────────▼────────────┐
                          │   Kong OSS API Gateway   │
                          │   Apollo Router (GQL)    │
                          └────────────┬────────────┘
                          ┌────────────▼────────────┐
                          │  PostgreSQL (per-tenant) │
                          │  NATS JetStream · Redis  │
                          └─────────────────────────┘
```

**Multi-tenancy:** schema-per-tenant isolation (`search_path = tenant_{slug}`)
**Auth:** JWT (15 min) + httpOnly refresh cookies (7 d) + MFA/TOTP
**Events:** NATS JetStream for cross-service messaging
**Background jobs:** BullMQ + Redis
**API:** REST + GraphQL (Apollo Federation 2)

---

## Repositories

### Backend services

| Repo | Description | Port |
|------|-------------|------|
| [core](https://github.com/cuencor/core) | Auth, Users, Tenants, RBAC, Devices, Branches, Modules | 3000 |
| [core-accounting](https://github.com/cuencor/core-accounting) | General Ledger, Journal Entries, Financial Reports | 3003 |
| [core-real-estate](https://github.com/cuencor/core-real-estate) | Projects, Lots, Sales, Installments, Cash Drawer | 3004 |
| [core-crm](https://github.com/cuencor/core-crm) | Canonical client registry | 3005 |
| [core-loans](https://github.com/cuencor/core-loans) | Loan products, disbursement, installments, payments | 3006 |

### Frontend apps

| Repo | Description | Port |
|------|-------------|------|
| [web](https://github.com/cuencor/web) | Angular 19 shell — host for all micro-frontends | 4200 |
| [web-accounting](https://github.com/cuencor/web-accounting) | Accounting MFE | 4201 |
| [web-real-estate](https://github.com/cuencor/web-real-estate) | Real Estate MFE | 4202 |
| [web-crm](https://github.com/cuencor/web-crm) | CRM MFE | 4203 |
| [web-loans](https://github.com/cuencor/web-loans) | Loans MFE | 4204 |

### Shared libraries (`@cuencor/*`)

| Package | Description |
|---------|-------------|
| [shared-types](https://github.com/cuencor/shared-types) | TypeScript types shared across frontend and backend |
| [shared-constants](https://github.com/cuencor/shared-constants) | Permissions, module IDs, NATS subjects, DB schemas |
| [backend-auth](https://github.com/cuencor/backend-auth) | NestJS guards, interceptors, middleware |
| [backend-database](https://github.com/cuencor/backend-database) | BaseTenantEntity, TenantContext, encryption utilities |
| [backend-messaging](https://github.com/cuencor/backend-messaging) | EmailModule (BullMQ + Nodemailer) |
| [frontend-auth](https://github.com/cuencor/frontend-auth) | Angular authGuard, permissionGuard, AuthStore |
| [ui-components](https://github.com/cuencor/ui-components) | Shared Angular component library |
| [styles](https://github.com/cuencor/styles) | Design system (SCSS, Indigo/Slate palette) |

---

## Tech stack

| Layer | Technology |
|-------|-----------|
| Backend framework | NestJS 11 + Fastify |
| Frontend framework | Angular 19 (standalone components, signals) |
| Micro-frontends | Native Federation |
| Database | PostgreSQL (TypeORM) |
| Messaging | NATS JetStream |
| Queue | BullMQ + Redis |
| API Gateway | Kong OSS 3.9 |
| GraphQL | Apollo Federation 2 |
| Auth | JWT · bcrypt · MFA/TOTP (otplib) |
| Security | Helmet · CORS · Throttler · ESLint (sonarjs + security) |

---

## Security & compliance

- OWASP Top 10 mitigations applied across all services
- SOC 2-aligned controls: rate limiting, input validation, audit logging, SSRF prevention
- MFA/TOTP available for all user accounts
- All dependencies audited on every CI run (`npm audit --audit-level=high`)

---

> Built with ❤️ for Latin American SMBs.
