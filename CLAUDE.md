# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

```bash
pnpm dev        # Start dev server (Turbopack enabled)
pnpm build      # Build for production
pnpm start      # Start production server
```

No lint or test scripts are configured. TypeScript checking is handled by the Next.js build.

## Database Setup

The database is seeded by hitting the `/seed` route while the dev server is running:

```
GET http://localhost:3000/seed
```

This creates four tables (`users`, `customers`, `invoices`, `revenue`) and populates them from `app/lib/placeholder-data.ts`. Uses `ON CONFLICT DO NOTHING`, so it's safe to run multiple times.

## Architecture

This is the [Next.js Learn Course](https://nextjs.org/learn) starter — a dashboard app called "Acme" built with the App Router.

**Key directories:**

- `app/lib/data.ts` — All database query functions (server-only). Direct SQL via the `postgres` package using `POSTGRES_URL` from env.
- `app/lib/definitions.ts` — All TypeScript types. Manually maintained (no ORM).
- `app/lib/utils.ts` — Shared helpers: `formatCurrency`, `formatDateToLocal`, `generateYAxis`, `generatePagination`.
- `app/ui/` — UI components grouped by feature (`dashboard/`, `invoices/`, `customers/`).
- `app/dashboard/` — Protected dashboard routes with a shared layout wrapping a `SideNav`.

**Database schema:**

| Table | Key fields |
|---|---|
| `users` | id (UUID), name, email, password (bcrypt hashed) |
| `customers` | id (UUID), name, email, image_url |
| `invoices` | id (UUID), customer_id, amount (cents, INT), status ('paid'|'pending'), date |
| `revenue` | month (VARCHAR, unique), revenue (INT) |

Invoice amounts are stored as **integer cents** throughout the DB and converted to dollars with `formatCurrency` (divides by 100) only at the display layer.

**Path alias:** `@/*` resolves to the project root, used throughout for imports.

**Auth:** `next-auth` v5 beta is installed but the sign-out button in `SideNav` is not yet wired up (stub form only).

**`app/query/route.ts`** is a commented-out scratch route for ad-hoc SQL queries during development — not used in production.
