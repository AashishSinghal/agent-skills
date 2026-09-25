---
name: site-visit-counter
description: Add Vercel Web Analytics and the shared visit counter to one of Aashish Singhal's web projects (aashishsinghal.com and its subdomains). Use when creating or deploying a new project on Vercel, or when the user asks to track visits, add analytics, or show a visit count on a site.
---

# Site visit counter and analytics

Every one of Aashish's sites gets two things, and nothing heavier (Google Analytics was
dropped as too much boilerplate):

1. **Vercel Web Analytics**: page views, referrers and countries in each project's Vercel
   dashboard. No keys.
2. **A visit counter**: a small "123 visits" label, backed by one shared API on the portfolio
   that stores every site's count in one Upstash Redis database.

Reference implementations: `portfolio-nextjs` (owns the API), `arcade` (Vite),
`coupon-generator` (Next.js). Copy from them rather than reinventing.

## How the counter works

- API: `https://aashishsinghal.com/api/visitor-count?site=<name>` (source:
  `portfolio-nextjs/api/visitor-count.ts`). `GET` returns `{ "count": n }`; `POST` increments
  `<name>:visits` and returns the new total. `400` for an unknown site, `503` if Redis isn't
  configured.
- CORS is limited to the origins listed for each site in the `SITES` map in that file.
- The client dedupes: at most one POST per browser per day (localStorage `visit-counted-on`),
  one shared request per page load (React StrictMode runs effects twice), and **no POSTs from
  local development**, only reads.
- The label renders nothing until a real number arrives, so it never shows a fake count.

## Steps

1. **Register the site** in `portfolio-nextjs/api/visitor-count.ts`: add one line to `SITES`,
   `"<name>": ["https://<name>.aashishsinghal.com"]`, using a short kebab-case name. Commit and
   push the portfolio **first**, so the API accepts the new origin before the site calls it.

2. **Add Vercel Web Analytics** to the new project with `@vercel/analytics`:
   - Vite / React SPA: `import { inject } from "@vercel/analytics"` and call `inject()` once in
     `src/main.tsx`. It tracks client-side route changes on its own.
   - Next.js (App Router): render `<Analytics />` from `@vercel/analytics/next` inside `<body>`
     in `app/layout.tsx`.
   - Other frameworks: see Vercel's Web Analytics quickstart for that framework.

3. **Add the counter component.** Copy `arcade/src/components/visit-count.tsx` (Vite) or
   `coupon-generator/src/components/VisitCount.tsx` (Next.js, `"use client"`). Change only the
   `site=` name in the URL, and in Next use `process.env.NODE_ENV === "production"` for "don't
   count in dev". Place it in the footer or header next to the "Source" link, and style it with
   the project's own tokens (small, muted, `tabular-nums`). Pluralise: "1 visit", "2 visits".

4. **Document it** in the project's `AGENTS.md`: where analytics and the counter live, the
   site name, and that the origin is registered in the portfolio's `SITES` map. If the project
   has a "no external services" rule, record this as a deliberate exception (it sends page
   visits only, never user data).

5. **Verify**, then commit and push (pushing `main` deploys):
   - Build and lint pass.
   - `curl "https://aashishsinghal.com/api/visitor-count?site=<name>"` returns `{"count":…}`.
   - `curl -i -H "Origin: https://<name>.aashishsinghal.com" "…?site=<name>"` includes
     `Access-Control-Allow-Origin` for that origin.
   - Avoid POSTing in tests. If you do, undo it with `DECR <name>:visits`, using the Upstash
     credentials from the portfolio's `.env` (never print them).

6. **Tell the owner the one manual step:** in Vercel, open the new project, go to the
   **Analytics** tab and click **Enable**. The counter itself needs no setup; the Redis
   credentials already live on the portfolio project.

## Gotchas

- **Vercel's tracking script ignores automated browsers** (`navigator.webdriver` or
  "Headless"), so DevTools-automation or Playwright checks never register a view. Ad
  blockers and privacy extensions also block `/_vercel/insights`. To confirm analytics work,
  open the site on a phone or a clean browser profile.
- A static site on its own subdomain needs no backend: it calls the portfolio API
  cross-origin. Don't give each site its own counter API or database.
- The counter is a vanity number, not tamper-proof. Anyone can POST, so don't treat it as
  real analytics; that's what Vercel Web Analytics is for.
