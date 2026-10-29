# Phase 1 — Project Audit & Implementation Plan
## SkinLab Aesthetics Demo + Beauty Salon CMS
**Date: 2026-10-29. Existing system inspected before any change.**

## 1. What already existed (do NOT rebuild)
- `index.html` — single self-contained demo site, gold/black/cream, ~11 KB, no framework, no backend, no database, no auth.
- Public sections already present: hero, treatments grid, transparent prices (from the clinic's publicly listed landing page), doctors (4 publicly named doctors), booking form (demo-only alert, sent nothing), visit/contact section, footer disclaimer.
- Brand: gold monogram + SKINLAB / AESTHETICS wordmark (text recreation in the demo, not the clinic's logo file), muted gold / black / cream.
- GitHub repo: `moin-hashmi-515/skinlab-aesthetics-demo` (public, main branch, created by the user 2026-10-29 after the GitHub App could not create repos — 403).

## 2. What was missing (this CMS adds)
- No admin dashboard, no service/package CRUD, no appointment workflow, no staff/customer records, no gallery/review/promo/blog management, no reports, no SEO/settings editing, no availability logic, no roles.
- Public booking did not persist anywhere, so admin could not see it.
- No page-content editing without editing source code.

## 3. Architecture chosen — and its hard limit
- **GitHub Pages is static hosting only.** It cannot run a server, database, secure login, real SMS/WhatsApp/email sending, or real payments.
- Therefore this delivery is a **working front-end demo CMS**: all data lives in the visitor's browser (`localStorage`, same origin as the demo site), all buttons/forms actually work in the browser, and availability/overlap logic runs in JavaScript.
- **This is NOT production-secure.** Role switching in the demo is UI-only. There is no server-side authorization, no password hashing, no MFA, and no real payment handling. Do NOT use this demo with real customer data, real bookings, or real payments.
- **Production path (separate work, needs a backend):** WordPress + booking plugin, or Node/Python + database + authenticated API, with server-side role checks, hashed passwords + MFA for admins, real WhatsApp/SMS/email provider credentials, and a real payment provider. Only then can appointments be truly confirmed, notifications truly sent, and payments truly tracked.

## 4. Files in this delivery
- `index.html` — public demo site (preserved, plus: hero text now reads CMS page settings from localStorage if present; booking form now saves a Requested appointment to localStorage instead of only showing an alert; still sends nothing to the clinic).
- `admin.html` — full CMS dashboard (all modules below, single file, no build step, no external dependencies).
- `CMS-PLAN.md` — this file.

## 5. Modules delivered (admin.html tabs)
- Dashboard: upcoming, pending, completed, cancellations, new customers, popular services, revenue — Collected (Paid, Completed only) kept separate from Unpaid bookings.
- Appointments: list + date filter + status filter + search, confirm / reject / cancel / complete / no-show, reschedule with availability re-check, staff assignment, notes, booking reference, payment status.
- Services: name, category, description, price PKR, promo price, duration (default 60 min — owner must confirm per service, durations were not publicly listed), active/inactive, staff eligibility, SEO title/description, image URL.
- Packages: bridal/party/beauty bundles, included services, price (seed packages are Draft with price to be set by owner — no fictional package prices invented), validity, terms, active/draft.
- Staff: 4 publicly named doctors seeded with Mon–Sat 12:00–20:00 hours (clinic hours as publicly listed); breaks, leave dates, service eligibility, assigned appointments — all editable.
- Customers: name, phone, email (optional), preferences, booking history derived from appointments. No customers seeded — no fictional customer data.
- Gallery: image URL, category, caption, alt text, before/after flag. Customer photos require the consent checkbox or they stay Draft and cannot be published.
- Reviews: submit / approve / publish / reject. No reviews seeded — no fake testimonials.
- Promotions: code, % or PKR amount, start/expiry, usage limit, eligibility, active/inactive.
- Blog: title, category, slug (auto), meta title/description, featured image URL, draft/published, date.
- Pages: hero heading/subtext, about text, contact details — applied to the public demo hero on the same origin; full page rendering in production needs a server/WordPress.
- Reports: date-filtered completed / cancelled / no-show, popular services, customer activity, Collected vs Unpaid revenue (never estimates presented as revenue).
- SEO: per-page meta title/description/canonical — stored in the demo; live meta tags in production need a server rebuild.
- Settings: business name, contact, address, currency PKR, timezone Asia/Karachi, opening hours, booking rules (lead time, advance limit, buffer, cancellation policy), notification channels (demo log only — nothing is actually sent until a provider is configured).
- Audit + Notifications log: every admin action logged with timestamp; notification entries are explicitly marked "Would send — NOT sent in demo".

## 6. Booking logic (client-side, demo)
- Availability = staff working hours + breaks + leave + existing Requested/Confirmed appointments + salon hours (Mon–Sat 12:00–20:00, Sun closed) + buffer minutes + lead time + advance limit.
- Overlap check runs on create AND reschedule: same staff, same date, overlapping [start, start + duration + buffer). "Any" staff picks the first eligible staff with no overlap; if none, booking is blocked with a clear reason.
- Statuses: Requested, Confirmed, Completed, Cancelled, No-Show. A booking is only "Confirmed" after an admin confirms it in the CMS — a public form submission is always "Requested".
- Timezone: Asia/Karachi throughout; dates/times are local date/time inputs, not UTC-converted.

## 7. Roles (demo UI only — see §3)
- Owner: all tabs. Manager: Dashboard, Appointments, Services, Packages, Staff, Customers, Reports. Receptionist: Dashboard, Appointments, Customers. Beautician: Dashboard + own Appointments only. Content Manager: Pages, Gallery, Reviews, Promotions, Blog, SEO. Customer: own Appointments only (filter by phone). Production must enforce this on the server — the demo cannot.

## 8. What was NOT built / still required for production
- No server, database, migrations, real auth, MFA, rate limiting, file uploads to a server, real notifications, real payments, POS, inventory, payroll, multi-branch.
- No automated test suite (no build/test framework in a static site). Manual tests performed are listed in §9.
- GitHub Pages must be enabled by the repo owner in Settings → Pages → Deploy from branch `main`, folder `/ (root)`, if it is not already on. The live URL will then be https://moin-hashmi-515.github.io/skinlab-aesthetics-demo/ — verify it loads before sharing it as live.

## 9. Tests performed (manual, in-browser logic checks)
- Service create/edit/publish/hide — form saves and table updates, persists on reload (localStorage).
- Public booking → appears in Admin Appointments as Requested with a reference; confirming changes Dashboard counts.
- Overlap: second appointment for same staff at overlapping time is blocked with a reason; different staff or non-overlapping time is allowed; reschedule re-checks.
- Outside hours (Sunday, before 12:00, after 20:00 minus duration) is blocked; past dates blocked.
- Gallery customer photo without consent stays Draft and cannot be published.
- Reports: Completed + Paid counts as Collected; Completed + Unpaid counts as Unpaid, never as Collected.
- Role switch hides tabs per §7 (UI only).
- Existing public site still loads: hero, treatments, prices, doctors, booking, contact unchanged in content except the booking persistence + hero override noted in §4.

## 10. Outstanding issues / owner decisions needed
- Confirm per-service durations, package prices, staff breaks/leave, and cancellation policy — defaults in the demo are editable placeholders, not clinic facts.
- Confirm whether the clinic wants a WordPress production build or a custom backend — that decides Phase 6 (production) scope and cost.
- Enable GitHub Pages (see §8) and verify the live URL.
- Never enter real customer phone numbers, photos, or payment details into this demo.
