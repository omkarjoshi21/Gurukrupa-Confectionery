# Gurukrupa Confectionery — B2B Web App & Management System

A mobile-first, **Marathi-language B2B web application and business management system** built for **Gurukrupa Confectionery** (Parbhani, Maharashtra) by **ByteLabs**.

This platform enables authorized wholesale buyers to browse box-to-box confectionery products, generate itemized PDF estimates using custom pricing rules, submit QR/UPI payment proofs (UTR + screenshot), and request appointments—while providing a role-based Admin Dashboard for daily operations.

---

## Tech Stack

- **Frontend:** React (Vite / TypeScript), Tailwind CSS (Orange & Yellow brand palette, Marathi UI)
- **Backend & Database:** Supabase (PostgreSQL, Row-Level Security, Storage)
- **Authentication:** 10-minute OTP Login for pre-authorized customer numbers + Role-based Staff/Admin Auth
- **PDF Generation:** Client/Server Marathi PDF Estimate Generator
- **Hosting & CI/CD:** Cloudflare Pages

---

## Core Features

### Customer Portal (Marathi UI)
- **Homepage:** Brand overview, quick navigation, booking & advance policy highlights, Monday weekly-off notice, and direct business contacts.
- **Product Catalog:** Box-to-box product listings with photos, company, category, box/unit details, availability badge, and multi-criteria filtering (`Company`, `Category`, `Rate Range`, `Availability`).
- **Estimate Builder & PDF Export:** Calculates totals dynamically and generates downloadable Marathi PDF estimates with unique Estimate Numbers.
- **QR / UPI Payment Proof Form:** Displays business QR/UPI details and collects UTR/Transaction Reference ID, payment screenshot upload, and self-attestation.
- **Appointment Scheduler:** Date/time appointment booking with **Mondays automatically disabled** (Weekly Off).

### Admin Dashboard & Operations
- **Overview & Reports:** Real-time metrics and reports for Customers, Products, Estimates, Pending Payments, Bookings, and Appointments.
- **Catalog & Rate Management:** Manage companies, categories, product availability, and base Company Rates.
- **Payment Verification:** Review uploaded screenshots and UTR numbers and update status (`Pending Verification` → `Verified` / `Rejected`).
- **Booking Lock & Modification Tracking:** Tracks `₹1,000` advance verification, `Booking Lock` state, post-lock modification fees (`1st Change: ₹5,000`, `2nd Change: ₹10,000`), and `₹2,00,000+` 100% advance rule.
- **User & Access Control:** Pre-authorize customer phone numbers and manage `Staff` and `Sales` accounts.

---

## Pricing & Business Logic Reference

| Rate Type | Formula | Example (Base = ₹100) |
| :--- | :--- | :--- |
| **Parbhani Rate** | `Company Rate + 5%` | `₹105` |
| **Direct Company** | `Company Rate + 2%` | `₹102` |
| **Purchiyon Delivery** | `Parbhani Rate + ₹20 / Box` | `₹125 / Box` |

> **Note:** Generating an estimate does not automatically confirm an order. Payment proofs are only considered verified after manual Admin/Staff approval.

---

## User Roles & Permissions (RBAC)

| Role | Capabilities | Restrictions |
| :--- | :--- | :--- |
| **Super Admin** | Full access to all modules, settings, users, and reports. | Only role permitted to **DELETE** records. |
| **Staff** | Manage products, rates, customers, estimates, payments, bookings, and appointments. | Cannot delete records or manage Super Admin settings. |
| **Sales** | Add new customer entries. | Restricted from rate edits, payment approvals, and admin data. |
| **Customer** | OTP login, view catalog, create estimate PDFs, submit payment proofs, request appointments. | Isolated to own account data only. |

---

## Suggested Project Structure

```text
gurukrupa-confectionery-b2b/
├── public/
│   ├── fonts/                  # Noto Sans Devanagari for crisp Marathi UI & PDF rendering
│   └── assets/                 # Business logo & static QR/brand assets
├── src/
│   ├── components/
│   │   ├── common/             # Navbar, Footer, WeeklyOffBanner, ProtectedRoute
│   │   ├── catalog/            # ProductCard, CatalogFilters, BoxQuantityInput
│   │   ├── estimate/           # EstimateSummary, EstimatePDFTemplate, PaymentProofForm
│   │   └── admin/              # StatCards, PaymentReviewModal, BookingLockBadge
│   ├── pages/
│   │   ├── public/             # HomePage, LoginPage (OTP)
│   │   ├── customer/           # CatalogPage, EstimatePage, AppointmentPage
│   │   └── admin/              # Dashboard, Products, Rates, Customers, Payments, Bookings, Reports
│   ├── lib/
│   │   ├── supabase.ts         # Supabase client & typed helpers
│   │   ├── pricing.ts          # Parbhani, Direct Company & Purchiyon calculation logic
│   │   └── pdfGenerator.ts     # Marathi PDF export utility
│   ├── hooks/                  # useAuth, useRole, useCatalog
│   └── types/                  # Database & domain TypeScript interfaces
├── supabase/
│   ├── migrations/             # SQL tables, triggers & booking lock constraints
│   └── policies/               # Row-Level Security (RLS) rules per role
├── .env.example
└── README.md
```

---

## Getting Started

### 1. Prerequisites
- Node.js `v18+` and `npm` / `pnpm`
- A [Supabase](https://supabase.com/) project (PostgreSQL + Storage buckets)
- Configured SMS OTP provider credentials (for 10-minute customer OTP login)

### 2. Installation
```bash
git clone https://github.com/<your-org>/gurukrupa-confectionery-b2b.git
cd gurukrupa-confectionery-b2b
npm install
```

### 3. Environment Variables
Copy `.env.example` to `.env.local` and fill in your credentials:

```env
VITE_SUPABASE_URL=https://your-project-id.supabase.co
VITE_SUPABASE_ANON_KEY=your-supabase-anon-key
VITE_OTP_EXPIRY_MINUTES=10
VITE_APP_LOCALE=mr
```

### 4. Run Locally
```bash
npm run dev
```

---

## Database & Storage Checklist (Supabase)

- [ ] **Tables:** `users`, `customers`, `companies`, `categories`, `products`, `estimates`, `estimate_items`, `payments`, `bookings`, `booking_changes`, `appointments`
- [ ] **Storage Buckets:**
  - `product-images` (Public read, Admin/Staff write, Super Admin delete)
  - `payment-proofs` (Private bucket — Customer upload, Admin/Staff signed-URL view only)
- [ ] **RLS Policies:** Verify `Staff` role has no `DELETE` policy on any table and `Customer` role is scoped strictly to `auth.uid()`.

---

## Deployment (Cloudflare Pages)

1. Connect this GitHub repository to **Cloudflare Pages**.
2. Set build command to `npm run build` and output directory to `dist`.
3. Add all `VITE_*` environment variables in Cloudflare Pages project settings.
4. Configure SPA fallback (`/* /index.html 200`) for client-side routing.

---

## Out of Scope (v1.0)
- Automated Payment Gateway Integration
- Promotional Schemes / Offer Engine
- Automated GPS / Delivery Tracking System
- Native Android or iOS Mobile Apps

---

**Prepared & Maintained By:** ByteLabs — Independent Software Studio