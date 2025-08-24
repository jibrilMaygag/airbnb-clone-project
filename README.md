# AirBnB Clone – Project README & Blueprint

> A full‑stack accommodation booking platform that lets users browse listings, view details, and complete bookings. Built with a modern frontend, a secure backend API, and a relational database. This document is your single source of truth for setup, architecture, workflows, and quality gates.

---

## Table of Contents

1. [Project Overview](#project-overview)
2. [Learning Objectives](#learning-objectives)
3. [Tech Stack](#tech-stack)
4. [Architecture](#architecture)
5. [Project Setup](#project-setup)
6. [Environment Variables](#environment-variables)
7. [Scripts](#scripts)
8. [Monorepo Structure](#monorepo-structure)
9. [UI/UX Planning](#uiux-planning)
10. [User Stories](#user-stories)
11. [Domain Model & Database](#domain-model--database)
12. [API Specification (REST)](#api-specification-rest)
13. [UI Component Library](#ui-component-library)
14. [State, Data Fetching & Caching](#state-data-fetching--caching)
15. [Testing Strategy](#testing-strategy)
16. [Accessibility (WCAG) Checklist](#accessibility-wcag-checklist)
17. [Performance Budget & Metrics](#performance-budget--metrics)
18. [Git Workflow & Branching](#git-workflow--branching)
19. [CI/CD Pipeline](#cicd-pipeline)
20. [Roles & Responsibilities](#roles--responsibilities)
21. [Milestones & Timeline](#milestones--timeline)
22. [Contribution Guidelines](#contribution-guidelines)
23. [Code Style & Linters](#code-style--linters)
24. [Review & Submission](#review--submission)
25. [License](#license)

---

## Project Overview

This project is a full‑stack clone of AirBnB with the following core capabilities:

* Browse a grid of property listings with powerful filters.
* View rich listing details (photos, amenities, map, reviews).
* Authenticate (sign up, sign in, reset password).
* Book a property with a secure checkout flow.
* Manage favorites and view past/future bookings.

**Goals**

* Implement responsive UI/UX based on Figma specs.
* Structure a real‑world web application end‑to‑end.
* Practice team collaboration with well‑defined roles.
* Adopt component‑based frontend architecture.
* Follow best practices for maintainability and quality.

---

## Learning Objectives

* Implement responsive UI/UX designs.
* Understand modular, layered architecture.
* Practice collaboration (Git, PRs, reviews, issues, sprints).
* Build reusable UI components.
* Apply best practices: accessibility, testing, documentation, CI/CD.

---

## Tech Stack

**Frontend**: React (Vite), TypeScript, Tailwind CSS, React Router, React Query (TanStack Query)

**Backend**: Node.js, Express, TypeScript (or NestJS), Zod for validation

**Database**: PostgreSQL with Prisma ORM (alternative: Sequelize or TypeORM)

**Auth**: JWT + HTTP‑only cookies (optional: OAuth via Google)

**Payments**: Stripe test mode (mock adapter possible for demo)

**Infrastructure**: Docker, GitHub Actions, Railway/Render/Vercel/Netlify

**Design**: Figma (UI kit + tokens)

**Testing**: Vitest + React Testing Library (frontend), Jest + Supertest (backend), Playwright (E2E)

**Tooling**: ESLint, Prettier, Husky, Lint‑Staged, Commitlint, Conventional Commits

> You can swap technologies if needed; keep the interfaces the same.

---

## Architecture

```
┌────────────┐      HTTPS       ┌──────────────┐      SQL      ┌─────────────┐
│   Client   │  ◄────────────►  │   API Tier   │ ◄──────────► │   Postgres  │
│  (React)   │                  │ (Express)    │              │   (RDBMS)   │
└────────────┘                  └──────────────┘              └─────────────┘
       ▲                                 ▲                            
       │                                 │                            
       └────────────── CDN  ─────────────┘                            
```

* **Separation of concerns**: UI, API, DB.
* **Validation**: Zod schemas at API boundaries.
* **Caching**: React Query for client‑side; HTTP caching headers for GET endpoints.
* **Security**: Helmet, rate limiting, input sanitization, prepared statements.

---

## Project Setup

### Prerequisites

* Node.js ≥ 20, pnpm or npm
* Docker (optional but recommended)
* PostgreSQL ≥ 14 (or Dockerized)

### Quick Start (Monorepo)

```bash
# 1) Clone
git clone https://github.com/<org>/<repo>.git
cd <repo>

# 2) Install dependencies (root workspaces)
pnpm install

# 3) Setup environment
cp apps/api/.env.example apps/api/.env
cp apps/web/.env.example apps/web/.env

# 4) Start dev (both apps)
pnpm dev
```

---

## Environment Variables

**apps/api/.env.example**

```env
# Server
PORT=4000
NODE_ENV=development
CORS_ORIGIN=http://localhost:5173

# Database
DATABASE_URL=postgresql://postgres:postgres@localhost:5432/airbnb_clone

# Auth
JWT_SECRET=replace_me
JWT_EXPIRES_IN=7d
COOKIE_SECURE=false

# Stripe (optional)
STRIPE_PUBLIC_KEY=pk_test_...
STRIPE_SECRET_KEY=sk_test_...
```

**apps/web/.env.example**

```env
VITE_API_URL=http://localhost:4000
VITE_STRIPE_PUBLIC_KEY=pk_test_...
```

---

## Scripts

**Root**

```json
{
  "scripts": {
    "dev": "turbo run dev --parallel",
    "build": "turbo run build",
    "test": "turbo run test",
    "lint": "turbo run lint",
    "format": "prettier --write ."
  }
}
```

**apps/web**

```json
{
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview",
    "test": "vitest",
    "lint": "eslint ."
  }
}
```

**apps/api**

```json
{
  "scripts": {
    "dev": "ts-node-dev src/index.ts",
    "build": "tsc -p .",
    "start": "node dist/index.js",
    "prisma:push": "prisma db push",
    "prisma:seed": "ts-node prisma/seed.ts",
    "test": "jest",
    "lint": "eslint ."
  }
}
```

---

## Monorepo Structure

```
.
├─ apps/
│  ├─ web/              # React app (Vite + TS)
│  └─ api/              # Express API (TS)
├─ packages/
│  ├─ ui/               # Shared UI components (optional)
│  └─ config/           # ESLint/Prettier/tsconfig shared
├─ .github/workflows/   # CI pipelines
├─ docs/                # ADRs, diagrams, decisions
└─ README.md
```

---

## UI/UX Planning

**Design Goals**

* Intuitive booking flow
* Visual consistency (design tokens)
* Fast loading, perceived performance
* Mobile‑first responsiveness

**Key Features**

* Property search & filtering
* Detailed property view
* Secure checkout
* User authentication

**Primary Pages**

| Page                  | Description                                   |
| --------------------- | --------------------------------------------- |
| Property Listing View | Grid of properties with filters               |
| Listing Detailed View | Photos, amenities, map, reviews, booking form |
| Simple Checkout View  | Payment + confirmation                        |

**Design Tokens (from Figma)**

* **Colors**: Primary `#FF5A5F`, Secondary `#008489`, Background `#FFFFFF`, Text `#222222`, Secondary Text `#717171`
* **Typography**: Circular (or Inter alternative)

  * Base: 16px, Medium (500)
  * Headings: 24–32px, Bold (700)
  * Secondary text: 14px, Book (400)

---

## User Stories

* As a visitor, I can browse listings and filter by location, price, dates, and capacity.
* As a visitor, I can view a listing’s details, photos, amenities, map, and reviews.
* As a user, I can sign up, sign in, sign out, and reset my password.
* As a user, I can favorite/unfavorite listings.
* As a user, I can start a booking, enter guest details, and complete payment.
* As a user, I can view my bookings and see status (upcoming, completed, canceled).
* As a host (phase 2), I can create/edit my listings and manage availability.

---

## Domain Model & Database

**Entities**

* `User(id, name, email, password_hash, avatar_url, role[guest|host|admin], created_at)`
* `Property(id, host_id→User, title, description, type, address, city, country, lat, lng, base_price, max_guests, bedrooms, beds, bathrooms, rating_avg, rating_count, created_at)`
* `Image(id, property_id→Property, url, alt, sort_order)`
* `Amenity(id, name, icon)`
* `PropertyAmenity(property_id→Property, amenity_id→Amenity)`
* `Availability(id, property_id→Property, date, is_available, price_override)`
* `Booking(id, user_id→User, property_id→Property, check_in, check_out, guests, total_price, status[pending|paid|canceled|refunded], created_at)`
* `Payment(id, booking_id→Booking, provider, provider_id, amount, currency, status[succeeded|failed|refunded], created_at)`
* `Review(id, property_id→Property, user_id→User, rating(1-5), comment, created_at)`
* `Favorite(user_id→User, property_id→Property, created_at)`

**Indexes & Constraints**

* Unique: `User.email`
* Composite unique: `Favorite(user_id, property_id)`
* Indexes: `Property(city)`, `Property(country)`, `Booking(check_in, check_out)`, `Review(property_id, created_at)`

---

## API Specification (REST)

**Auth**

* `POST /auth/register` → {name, email, password}
* `POST /auth/login` → {email, password} (sets HTTP‑only cookie)
* `POST /auth/logout`
* `POST /auth/forgot-password` → {email}
* `POST /auth/reset-password` → {token, newPassword}

**Listings**

* `GET /properties?city=&country=&minPrice=&maxPrice=&checkIn=&checkOut=&guests=`
* `GET /properties/:id`
* `GET /properties/:id/reviews`

**Favorites**

* `GET /me/favorites`
* `POST /me/favorites/:propertyId`
* `DELETE /me/favorites/:propertyId`

**Bookings**

* `POST /bookings` → {propertyId, checkIn, checkOut, guests}
* `GET /me/bookings`
* `GET /bookings/:id`
* `POST /bookings/:id/cancel`

**Payments (Stripe)**

* `POST /payments/create-intent` → {bookingId}
* `POST /webhooks/stripe`

**Errors**

* JSON problem‑details: `{ status, code, message, details }`

**Validation**

* Zod schemas at request/response boundaries.

---

## UI Component Library

**Navbar**

* Logo, search bar, user menu (login/signup/profile), responsive drawer

**Property Card**

* Image, price/night, location, rating, favorite toggle

**Filters**

* Location, date range, price slider, guests, property type

**Listing Details**

* Image gallery, amenities list, map, host card, reviews, booking form

**Checkout**

* Summary, guest details, payment form, confirmation

**Footer**

* Site links, company info, social links, copyright

> All components are responsive, accessible, and reusable.

---

## State, Data Fetching & Caching

* **React Query** for server state (caching, retries, loading states).
* **URL‑driven filters** (query params for shareable results).
* **Optimistic updates** for favorites.
* **Skeletons** + **Suspense** for perceived performance.

---

## Testing Strategy

**Unit**

* Frontend: Vitest + RTL (components, hooks)
* Backend: Jest (services, controllers)

**Integration**

* Backend routes with Supertest (DB in Docker + migrations)

**E2E**

* Playwright: auth, search, view listing, book, pay, confirm

**Coverage Goals**

* 80% statements/branches minimum for core modules

---

## Accessibility (WCAG) Checklist

* Keyboard navigable, visible focus, skip links
* Color contrast ≥ 4.5:1 (text vs background)
* Semantic HTML (landmarks, headings)
* ARIA only when necessary
* Form labels, error messages, validation hints
* Alt text for all non‑decorative images

---

## Performance Budget & Metrics

* LCP < 2.5s (3G Fast), TTI < 3.5s
* JS < 200KB gzipped on landing
* Images lazy‑loaded, responsive `srcset`
* Use code‑splitting and route‑based chunks

---

## Git Workflow & Branching

* Default branch: `main`
* Branches: `feat/*`, `fix/*`, `chore/*`, `docs/*`
* Conventional Commits: `feat:`, `fix:`, `refactor:`, `docs:`, etc.
* PR checks: build, lint, test, typecheck, preview deploy

---

## CI/CD Pipeline

**.github/workflows/ci.yml** (excerpt)

```yaml
name: CI
on: [push, pull_request]
jobs:
  build-test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: 20 }
      - run: corepack enable
      - run: pnpm install --frozen-lockfile
      - run: pnpm lint
      - run: pnpm build
      - run: pnpm test -- --ci
```

**Deploy**

* Web → Vercel/Netlify; API + DB → Railway/Render + managed Postgres
* Preview envs per PR

---

## Roles & Responsibilities

| Role            | Responsibilities                               |
| --------------- | ---------------------------------------------- |
| Project Manager | Timeline, standups, scope, delivery, risk mgmt |
| Product Owner   | Requirements, backlog, priorities, acceptance  |
| Scrum Master    | Facilitates agile ceremonies, removes blockers |
| Designers       | Figma mockups, design tokens, UX quality       |
| Frontend Devs   | Components, routes, state, accessibility       |
| Backend Devs    | APIs, DB schema, auth, payments, observability |
| QA/Testers      | Test plans, manual + E2E, bug triage           |
| DevOps Eng      | CI/CD, environments, infra as code, monitoring |

---

## Milestones & Timeline

**Milestone 0 – Project Initialization (Week 1)**

* Repo + README + issue templates + CI skeleton
* Figma audit, design tokens extracted

**Milestone 1 – UI Scaffolding (Weeks 2‑3)**

* Navbar, Property Card, Footer, routing, layout grid
* Listing page with filters (stub data)

**Milestone 2 – API & DB (Weeks 3‑4)**

* Prisma schema, migrations, seed data
* Properties `GET`, property `GET/:id`, auth endpoints

**Milestone 3 – Booking Flow (Weeks 5‑6)**

* Booking creation, availability checks, Stripe intent
* Checkout UI + confirmation page

**Milestone 4 – Polish & QA (Weeks 7‑8)**

* Favorites, reviews, performance passes, A11y fixes
* E2E suite + bug bash + docs finalization

---

## Contribution Guidelines

* Open an issue before large changes.
* Link PRs to issues, keep PRs < 400 LOC when possible.
* Write/update tests and docs with features.
* Use draft PRs early; request review when ready.

**Issue Templates** (titles)

* `feat: <short summary>`
* `fix: <short summary>`
* `chore: <short summary>`

---

## Code Style & Linters

* **ESLint**: `@typescript-eslint` + React rules
* **Prettier**: width 100, semi, single quotes
* **Husky**: pre‑commit `lint-staged` (lint & format)
* **Commitlint**: enforce Conventional Commits

---

## Review & Submission

✅ Complete the project by the deadline.
📄 Submit all required files (see below).
🔗 Generate a review link (project README + live demo URLs).
👥 Request peer reviews (2+ peers recommended).

**Auto‑check Core Files**

* `/apps/web/src/main.tsx`, `/apps/web/src/pages/*`
* `/apps/api/src/index.ts`, `/apps/api/src/routes/*`
* `/prisma/schema.prisma`
* `/docs/architecture.md`, `/README.md`
* CI workflow under `.github/workflows/`

---

## License

MIT © Your Team

---

### Appendix A: Sample Prisma Schema (Excerpt)

```prisma
datasource db { provider = "postgresql" url = env("DATABASE_URL") }
generator client { provider = "prisma-client-js" }

model User {
  id           String   @id @default(cuid())
  name         String
  email        String   @unique
  passwordHash String
  role         Role     @default(GUEST)
  avatarUrl    String?  
  createdAt    DateTime @default(now())
  bookings     Booking[]
  reviews      Review[]
  favorites    Favorite[]
}

enum Role { GUEST HOST ADMIN }

model Property {
  id           String   @id @default(cuid())
  host         User     @relation(fields: [hostId], references: [id])
  hostId       String
  title        String
  description  String
  type         String
  address      String
  city         String
  country      String
  lat          Float
  lng          Float
  basePrice    Int
  maxGuests    Int
  bedrooms     Int
  beds         Int
  bathrooms    Int
  ratingAvg    Float    @default(0)
  ratingCount  Int      @default(0)
  createdAt    DateTime @default(now())
  images       Image[]
  amenities    PropertyAmenity[]
  bookings     Booking[]
  reviews      Review[]
  favorites    Favorite[]
}

model Image {
  id         String   @id @default(cuid())
  property   Property @relation(fields: [propertyId], references: [id])
  propertyId String
  url        String
  alt        String?
  sortOrder  Int       @default(0)
}

model Amenity {
  id    String @id @default(cuid())
  name  String @unique
  icon  String?
  props PropertyAmenity[]
}

model PropertyAmenity {
  property   Property @relation(fields: [propertyId], references: [id])
  propertyId String
  amenity    Amenity  @relation(fields: [amenityId], references: [id])
  amenityId  String

  @@id([propertyId, amenityId])
}

model Booking {
  id          String   @id @default(cuid())
  user        User     @relation(fields: [userId], references: [id])
  userId      String
  property    Property @relation(fields: [propertyId], references: [id])
  propertyId  String
  checkIn     DateTime
  checkOut    DateTime
  guests      Int
  totalPrice  Int
  status      BookingStatus @default(PENDING)
  createdAt   DateTime @default(now())
  payment     Payment?
}

enum BookingStatus { PENDING PAID CANCELED REFUNDED }

model Payment {
  id         String   @id @default(cuid())
  booking    Booking  @relation(fields: [bookingId], references: [id])
  bookingId  String   @unique
  provider   String
  providerId String?
  amount     Int
  currency   String   @default("USD")
  status     String
  createdAt  DateTime @default(now())
}

model Review {
  id         String   @id @default(cuid())
  property   Property @relation(fields: [propertyId], references: [id])
  propertyId String
  user       User     @relation(fields: [userId], references: [id])
  userId     String
  rating     Int
  comment    String?
  createdAt  DateTime @default(now())
}

model Favorite {
  user       User     @relation(fields: [userId], references: [id])
  userId     String
  property   Property @relation(fields: [propertyId], references: [id])
  propertyId String
  createdAt  DateTime @default(now())

  @@id([userId, propertyId])
}
```

### Appendix B: Example Component Checklist

* [ ] Navbar: keyboard nav, aria labels, responsive
* [ ] Property Card: image aspect ratio, lazy load, skeleton
* [ ] Filters: accessible sliders, dates (aria‑live), clear all
* [ ] Gallery: keyboard‑friendly lightbox
* [ ] Map: fallback for users denying geolocation
* [ ] Booking Form: field validation, error summaries
* [ ] Checkout: 3‑D Secure test flows (Stripe)

### Appendix C: Sample Seed Script Outline

* Users: 10 demo accounts (hashed passwords)
* Properties: 100 across 5 cities, with amenity mixes
* Reviews: 2–5 per property, randomized ratings
* Favorites: 0–10 per user
* Availability: next 6 months per property

### Appendix D: Definition of Done (per story)

* Unit tests updated/added and passing
* Types stable, no `any` leaks
* A11y check (axe) and manual keyboard pass
* CLS/LCP budget respected on target pages
* Docs updated (README/ADR/changelog)
* Preview deployment added to PR
