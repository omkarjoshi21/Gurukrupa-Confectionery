# Gurukrupa Confectionery — Developer & Team Implementation Brief

**Project:** B2B Confectionery Catalog, Estimate & Order Management Web App  
**Client:** Gurukrupa Confectionery (Parbhani, Maharashtra)  
**Prepared By:** ByteLabs — Independent Software Studio  
**Version:** v1.0 (Internal Team & Developer Handoff Guide)  

---

## 1. Project Overview (What We Are Building)
We are building a **Marathi-only, mobile-first B2B web application** for a wholesale confectionery business. It is **not** a static brochure site—it is a database-backed web app with role-based access control, dynamic pricing rules, PDF estimate generation, manual UPI/QR payment verification, booking lock/change rules, and an Admin Dashboard.

* **UI Language:** Strictly **Marathi** across all customer and admin screens (keep labels clear, consistent, and mobile-friendly).
* **Color Theme:** **Orange + Yellow** (matching the client's brand and 5 reference design photos).
* **Target Devices:** Mobile-first Responsive Web App (Android/iOS browsers, tablets, and desktop).
* **Proposed Tech Stack:** React + Supabase (PostgreSQL, Auth, Storage) + Cloudflare Pages (free/low-cost tier).

---

## 2. User Roles & Permissions Matrix (RBAC)
Enforce role permissions both in the UI and via **Supabase Row-Level Security (RLS) / Backend checks**:

| Role | What They Can Do | Strict Restrictions |
| :--- | :--- | :--- |
| **Super Admin** | Full access to everything: Dashboard, Users, Products, Rates, Customers, Payments, Bookings, Appointments, Reports, Settings. **Only role allowed to DELETE records.** | None |
| **Staff** | Day-to-day operations: Add/Edit Products & Rates, Manage Customers, view Estimates, Verify/Reject Payments, Manage Bookings & Appointments, view Reports. | **Cannot DELETE** products, users, or records. |
| **Sales** | Add new Customers (`Customer Add`). | Cannot edit rates, verify payments, or access full admin data *(final visibility details pending client Q#15)*. |
| **Customer** | OTP Login (authorized numbers only), browse Catalog, generate Estimate PDFs, submit QR/UPI Payment Proof, book Appointments. | Cannot view admin data or any other customer's records. |

---

## 3. Core Pages & Feature Breakdown

### Page 1: Homepage (Public / Entry)
* Business Logo, Name (`Gurukrupa Confectionery`), short intro, and main banner.
* Quick navigation cards/buttons to **Product Catalog**, **Estimate Bill**, and **Appointment**.
* Highlighted **Booking & Advance Payment Rules** banner.
* **Weekly Off Notice:** **Monday Closed** (सोमवार Weekly Off).
* **Contact Details Displayed:**
  * Nitin Seth: `9370730480`
  * Devadas: `8888379585`
  * Gouse Bhai: `8888379616`

### Page 2: Product Catalog
* **Product Card Fields:** Product Name, Photo, Company, Category, Box/Unit size info, Applicable Rate, and Status badge (`Available` / `Not Available`).
* **Filters:** Filter by `Company`, `Category`, `Rate Range`, and `Availability`.
* **Sales Rule:** **Box-to-Box Only** (no loose pieces; quantity input must validate whole boxes and minimum box quantity once confirmed by client).
* **Admin/Staff Controls:** Add product, edit details/photo/rate, toggle `Available` / `Not Available`. Only **Super Admin** can delete.

### Page 3: Estimate Bill & Payment Proof Form
1. **Dynamic Pricing Engine (Must validate on Backend):**
   * **Parbhani Rate:** `Company Rate + 5%` *(Example: ₹100 + 5% = ₹105)*
   * **Direct Company Rate:** `Company Rate + 2%` *(Example: ₹100 + 2% = ₹102)*
   * **Purchiyon Delivery:** `Parbhani Rate + ₹20 per Box` *(Example: ₹105 + ₹20 = ₹125/Box)*
2. **Estimate Generation & PDF Export:**
   * Fields: `Estimate Number` (auto-increment/unique ID), `Date`, `Customer Name`, `Mobile Number`, itemized `Product`, `Company`, `Box Quantity`, `Applied Rate`, `Delivery Charge` (if applicable), `Grand Total`, `Applicable Advance Amount`, and `Payment Instructions`.
   * Must support **in-browser view + downloadable PDF** in Marathi.
   * **Important Logic:** Creating an estimate does **not** automatically confirm an order.
3. **Payment Proof Submission Form:**
   * Display business **QR Code & UPI ID**.
   * Customer inputs: `UTR / Transaction Reference Number`, `Payment Screenshot Upload` (store securely in Supabase Storage), and a mandatory `Self-Attestation Checkbox`.
   * Default Status: `Pending Verification` (Admin/Staff manually changes to `Verified` or `Rejected`).

### Page 4: Appointment Booking
* **Form Fields:** Customer Name, Mobile Number, `Appointment Date`, `Preferred Time`, `Purpose / Reason`, and optional `Message`.
* **Validation Rule:** Disable **Mondays** on the date picker (`Weekly Off`).
* **Workflow Statuses:** `Pending` → `Approved` / `Rejected` → `Completed`.

### Page 5: OTP Login & Mobile Experience
* **Pre-Authorized Access Only:** A customer can only request an OTP if their mobile number has already been added/approved by Admin (or Sales).
* **OTP Expiry:** **10 minutes** strict validity.
* Note: Keep the SMS provider integration modular so we can plug in whichever SMS gateway the client pays for.

---

## 4. Critical Business & Booking Rules (Backend Validation)
All financial and booking state transitions must be enforced on the backend:

1. **Booking Lock:** Triggered only after Admin marks the **₹1,000 Advance Payment** as `Verified`.
2. **Post-Lock Modification Penalties:**
   * **1st Change:** `₹5,000` (Non-Refundable)
   * **2nd Change:** `₹10,000` (Non-Refundable)
   * *(3rd+ change rules pending client Q#8)*
3. **High-Value Orders (`₹2,00,000+`):** Requires **100% Advance Payment** verification.

---

## 5. Admin Dashboard Modules
Build a clean, tabbed/sidebar Admin Panel covering these 10 sections:
1. **Dashboard Overview:** Summary cards for Customers, Products, Estimates, Pending Payments, Bookings, and Appointments.
2. **Product, Company & Category Management**
3. **Rate Management:** Base Company Rates + markup rule configuration.
4. **Customer Management:** Add/approve authorized mobile numbers.
5. **Estimate Management:** View all customer estimates & download PDFs.
6. **Payment Management:** Inspect UTR + uploaded screenshots, approve (`Verified`) or `Rejected`.
7. **Booking Management:** Track advance received, `Booking Lock` state, and `1st / 2nd Change` logs.
8. **Appointment Management:** Approve/reject/complete requests.
9. **User Management:** Create `Staff` and `Sales` accounts and assign roles.
10. **Reports:** Filterable summary exports for Customers, Estimates, Payments, Bookings, and Appointments.

---

## 6. Explicitly Out of Scope (Do Not Build)
To protect our timeline and budget, **do not** build any of the following:
* Automated Payment Gateway integration (Razorpay/Stripe/etc.)
* Schemes / Discounts / Offer engine
* Live Delivery / GPS Tracking system
* Native Android or iOS mobile apps (Web/PWA only)

---

## 7. Development Roadmap & Milestones
* **Phase 1 — Scope & Assets Lock:** Wait for client answers to the 22 scope questions + Master PDF + 5 Design Photos + Logo + Product/Rate Excel/CSV.
* **Phase 2 — Database & Auth Setup:** Set up PostgreSQL schema, RLS policies for all 4 roles, Storage buckets (product images & payment proofs), and 10-min OTP flow.
* **Phase 3 — Customer App (Mobile-First):** Homepage, Catalog + Filters, Pricing Calculator, Marathi Estimate PDF generator, Payment Proof upload, Appointment form.
* **Phase 4 — Admin Dashboard & Booking Logic:** Build the 10 dashboard modules, payment verification workflow, and booking lock/change counters.
* **Phase 5 — QA & Handover:** Test role restrictions (ensure Staff cannot delete), test Marathi PDF rendering on mobile devices, deploy to Cloudflare Pages, and hand over credentials.

---

## 8. Pending Client Clarifications (Hold Complex Logic Until Answered)
Before coding edge cases, check the client's answers to these key questions from Section 14 of the Scope Doc:
* **Pricing & Visibility:** Are product rates visible before login or only after OTP login? (Q#5) Who selects `Parbhani Rate` vs `Direct Company Rate`—Customer or Admin? (Q#4) What is the exact rule for `Purchiyon Delivery`? (Q#3)
* **Snapshot Pricing:** If an Admin updates a product's rate later, should previously generated Estimates keep their old locked rate? (Q#13 — *Recommended: Yes, store a rate snapshot on estimate line items*).
* **Booking & Penalty Triggers:** Is the ₹1,000 advance refundable or non-refundable? (Q#6) Are the ₹5,000 / ₹10,000 change fees charged on top of the advance? (Q#7) What counts as a "change"—quantity, product swap, or date? (Q#9)
* **Customer History & Re-uploads:** Can customers view past estimates/bookings in their profile? (Q#12) If a payment proof is `Rejected`, can they re-submit a new UTR/screenshot on the same estimate? (Q#17)