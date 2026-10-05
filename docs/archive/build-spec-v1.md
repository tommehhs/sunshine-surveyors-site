> **Archived 2026-10-05.** Recovered word-for-word from the Claude chat "Launching Sunshine Surveyors trading name" (2026-07-06). **Not built.** The live site took a different direction (own services, no cadastral work). Check `../roadmap.md` before acting on anything here.

# Sunshine Surveyors — Build Spec v1

Trading name of Brimbank Spatial Pty Ltd. Hyperlocal lead-generation site for land surveying (launch) and town planning reports (built now, switched on later). Target market: homeowners and small developers across the City of Brimbank and Melbourne's west.

**Scope decision: Option C** — architecture supports two service categories (surveying, planning) from day one; only surveying is publicly visible at launch. Planning goes live via a feature flag, no rebuild.

---

## 1. Positioning & strategy

- **Model**: capture local search intent → qualified enquiry → route to licensed fulfilment partner (target partner: SMC Land Surveyors, Sunshine; backup: Prime Land Consultants, Kensington). Sunshine Surveyors owns the brand, site, and lead flow; licensed surveyors perform the work.
- **Reference synthesis**:
  - Landtek → conversion layout (hero quote form, fixed pricing, 1-hour response, 48h turnaround, click-to-call)
  - JAC Surveyors → credibility structure (projects/case studies, named testimonials, trust signals)
  - Planna → adjacency strategy (planning reports as second category; whole-approval-journey framing)
  - Flat Out / Prime → what to beat: thin geo pages, generic SEO copy, no local data
- **Defensible edge**: ~80 programmatic service × suburb pages enriched with real Brimbank data (ABS Census + council open datasets) that ad-buying competitors can't cheaply replicate.
- **Compliance copy rule**: all marketing must make clear that surveys are carried out by licensed surveyors (e.g. "All cadastral work performed by Licensed Surveyors registered with the Surveyors Registration Board of Victoria"). This line appears in the footer sitewide and on every service page. No claim that Sunshine Surveyors itself is a licensed surveying firm.

## 2. Tech stack

- **Framework**: Next.js (App Router), static generation (SSG) with ISR for content pages
- **Styling**: Tailwind CSS
- **Data/CMS**: Supabase (Postgres) — content, suburb data, leads. No content in the codebase; pages regenerate from Supabase so routine updates never require a redeploy.
- **Hosting**: GitHub → Vercel auto-deploy on push
- **Forms**: Next.js route handler → Supabase insert → email notification (Resend or similar) → auto-acknowledgment to enquirer
- **Analytics**: Vercel Analytics + Google Search Console from day one; GA4 optional

## 3. Supabase schema

```sql
-- Service categories: 'surveying' (live), 'planning' (built, hidden)
service_categories (
  id uuid pk, slug text unique, name text,
  is_live boolean default false, sort int
)

services (
  id uuid pk, category_id fk, slug text unique,
  name text,                 -- "Boundary Re-establishment Survey"
  plain_name text,           -- "Find your true property boundaries"
  description text, homeowner_explainer text,  -- plain-English "when you need this"
  price_from int, price_note text, turnaround text,
  faqs jsonb, is_live boolean, sort int
)

suburbs (
  id uuid pk, slug text unique, name text, postcode text,
  lga text default 'Brimbank',
  lat numeric, lng numeric,
  census jsonb,        -- population, dwellings, median lot context, growth
  local_context jsonb, -- from council datasets: parks, zoning notes, subdivision activity
  intro_copy text,     -- unique per-suburb editorial paragraph
  is_live boolean, sort int
)

-- The programmatic matrix; row exists only when combination should publish
suburb_service_pages (
  id uuid pk, suburb_id fk, service_id fk,
  unique_copy text,          -- suburb-specific angle for this service
  local_stats jsonb,         -- injected data points rendered on page
  meta_title text, meta_description text,
  is_live boolean
)

leads (
  id uuid pk, created_at timestamptz default now(),
  name text, phone text, email text,
  suburb_slug text, service_slug text,
  property_address text, message text,
  source_page text, utm jsonb,
  status text default 'new',   -- new / contacted / sent_to_partner / won / lost
  partner_id fk null
)

partners (
  id uuid pk, name text, contact_name text, phone text, email text,
  licence_no text, suburbs_covered text[], services_covered text[],
  deal_type text,  -- per_lead / monthly / rev_share
  is_active boolean
)

testimonials (id uuid pk, author text, suburb text, service_slug text, quote text, rating int, is_live boolean)

projects (id uuid pk, title text, suburb text, service_slug text, summary text, image_url text, is_live boolean)
```

## 4. Sitemap & page templates

### Static pages
- `/` Home
- `/services` Services index (live categories only)
- `/services/[service]` Service page × ~5 at launch
- `/suburbs` Suburb index (map + list)
- `/suburbs/[suburb]` Suburb hub page × ~20
- `/[service]/[suburb]` Service×suburb landing page × ~80 (the SEO engine)
- `/projects`, `/about`, `/contact`, `/quote` (standalone form page for ad traffic later)
- `/privacy`, `/terms`

### Launch services (category: surveying)
1. Boundary / Title Re-establishment Survey — "confirm your true boundaries before you build, fence or subdivide"
2. Subdivision Survey
3. Feature & Level Survey (for architects/designers/renovations)
4. Set-out Survey (new builds)
5. Site / Land Survey (general catch-all page targeting broad terms)

### Built-but-hidden services (category: planning, is_live=false)
6. Town Planning Report (VIC council applications)
7. Feasibility / Preliminary Planning Assessment

### Launch suburbs (~20, City of Brimbank)
Sunshine, Sunshine North, Sunshine West, St Albans, Deer Park, Derrimut, Ardeer, Albion, Cairnlea, Keilor Downs, Keilor Park, Keilor, Kealba, Kings Park, Delahey, Sydenham, Taylors Lakes, Albanvale, Hillside, Brooklyn (Brimbank part)

### Template: `/[service]/[suburb]` (the money page)
1. H1: "{Service} in {Suburb}" + subhead in homeowner language
2. Quote form above the fold (right column desktop / immediately below H1 mobile)
3. Trust bar: Licensed Surveyors · Fixed upfront pricing · Fast turnaround · Local to Brimbank
4. "When you need this in {Suburb}" — unique_copy from Supabase
5. Local data block — 3–5 real stats rendered from local_stats (e.g. dwellings, typical lot era, recent subdivision activity, zoning notes). **This block is the anti-doorway-page differentiator; no page publishes without populated local_stats.**
6. Pricing anchor: "from ${price_from}" + what affects price
7. Process: 4 steps (enquire → fixed quote in 1 business hour → survey booked → plan delivered)
8. FAQs (service-level + 1–2 suburb-specific)
9. Testimonial (matched by service/suburb where available)
10. Cross-links: same service in neighbouring suburbs; other services in this suburb
11. Footer compliance line

### Template: suburb hub `/suburbs/[suburb]`
Suburb intro (unique editorial), map, Census snapshot, all services offered there, recent projects, enquiry form. This page also seeds the future Brimbank Maps cross-link.

## 5. Conversion elements (sitewide)

- Sticky header: phone number (tel: link), "Get a fixed quote" button
- Hero form fields (keep to 5): name, phone, suburb (select), service (select), message (optional). Email optional at first touch — phone-first market.
- Promise set: response within 1 business hour (7am–7pm Mon–Sat), fixed quote, no obligation
- Mobile: sticky bottom bar with Call + Quote buttons
- Thank-you page sets expectation ("we'll call within the hour") + triggers auto-ack email/SMS

## 6. SEO technical requirements

- Metadata generated per page from Supabase (meta_title pattern: "{Service} {Suburb} | Fixed Price, Fast Turnaround | Sunshine Surveyors")
- JSON-LD: `LocalBusiness` sitewide + `Service` on service pages + `FAQPage` where FAQs render
- `sitemap.xml` auto-generated from live rows; `robots.txt`
- Canonicals on every page; suburb×service pages canonical to themselves
- Internal linking: every page reachable within 3 clicks; suburb hubs link all their service pages
- Core Web Vitals: static generation, next/image, no blocking scripts — target green across the board
- Day-one checklist: Google Business Profile (service-area business), Search Console verification + sitemap submission, Bing Webmaster

## 7. Lead automation

1. Form submit → insert into `leads` with source_page + UTM
2. Instant email (later SMS) notification to owner
3. Auto-acknowledgment to enquirer
4. Status workflow in Supabase (new → contacted → sent_to_partner → won/lost)
5. Phase 2: auto-route by suburb/service to matching active partner; weekly lead digest (Cowork job)

## 8. Copy & design direction

- **Voice**: plain-English, homeowner-first. Lead with the problem ("Not sure where your boundary actually is?") not the jargon. Active voice, sentence case, specific promises. Every service page answers: what is it, when do I need it, what does it cost, how fast.
- **Design**: modern and fast where the whole niche is dated Wix/PHP templates — that alone differentiates. Clean layout, strong type hierarchy, one signature visual element drawn from the subject: a subtle cadastral/boundary-line motif (fine survey-mark and lot-line linework) used in the hero and section dividers. Avoid generic AI-default palettes; anchor the palette in surveying's own world (e.g. deep field-book green or ink navy + high-vis accent used sparingly). Mobile-first — most "surveyor near me" searches are on phones.
- **Imagery**: real Brimbank imagery over stock wherever possible; maps as visual assets (you have the data).

## 9. Build order (Claude Code phases)

1. **Scaffold**: Next.js + Tailwind + Supabase client, repo → GitHub → Vercel pipeline live with a holding page
2. **Schema + seed**: run schema, seed service_categories, 7 services, 20 suburbs (Census jsonb from ABS, local_context from council datasets), generate suburb_service_pages rows for surveying×suburbs
3. **Templates**: build the 4 page templates + static pages, wired to Supabase
4. **Forms + automation**: lead capture, notifications, auto-ack, thank-you
5. **SEO layer**: metadata, JSON-LD, sitemap, robots, internal linking pass
6. **Content pass**: unique_copy + local_stats for every publishing page (no thin pages go live)
7. **Launch**: domain DNS → Vercel, GBP + Search Console, submit sitemap

## 10. Kickoff prompt for Claude Code

> Build a lead-generation website for "Sunshine Surveyors" (trading name of Brimbank Spatial Pty Ltd) per the attached spec (sunshine-surveyors-build-spec.md). Stack: Next.js App Router + Tailwind + Supabase, deployed via GitHub → Vercel. Start with Phase 1 (scaffold + deploy pipeline) and Phase 2 (Supabase schema + seed data) from section 9. Key constraints: all content lives in Supabase, not the codebase; two service categories exist but only "surveying" is live; every service×suburb page must render real local data from the local_stats field and must not publish without it; the footer compliance line about Licensed Surveyors appears sitewide. Ask me for the Supabase project URL and keys, then proceed phase by phase, confirming after each.

---

*v1 — 6 July 2026. Update this file as decisions change; it's the single source of truth for the build.*
