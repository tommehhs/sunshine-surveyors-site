# Build spec v1 — summary (2026-07-06)

**Status: not built.** Superseded in practice by the one-page site launched 2026-07-07, which took a different direction (own services, no cadastral). Kept for reference in case direction B or C is chosen (see `../roadmap.md`).

The full spec is in `build-spec-v1.md` next to this file (recovered from the original chat on 2026-10-05).

## Model
Capture local search intent → qualified enquiry → route to a licensed partner (SMC Land Surveyors; backup Prime Land Consultants). All marketing attributes regulated work to the registered licensee.

## Scope decision ("Option C")
Built for two service categories (surveying and town planning reports). Only surveying goes live at launch; planning switched on later by a feature flag, with no rebuild.

## Stack
Next.js App Router, Tailwind CSS, Supabase (Postgres), GitHub → Vercel. All content stored in Supabase, not in code.

## Sitemap
~80 service × suburb pages across ~20 Brimbank suburbs, plus service pages, suburb pages and static pages. **Rule: no page publishes without real local data** (Census stats, council datasets), so pages aren't thin or doorway content.

## Build phases
1. Scaffold + deploy pipeline
2. Supabase schema + seed (services, suburbs, Census data)
3. Page templates wired to Supabase
4. Forms + lead automation (notify Tommy, auto-acknowledge enquirer)
5. SEO layer (metadata, JSON-LD, sitemap, internal links)
6. Content pass (unique copy + local stats per page)
7. Launch (DNS, Google Business Profile, Search Console)

## Design direction
Mobile-first; deep navy or field-book green with a sparing high-vis accent; real Brimbank imagery and maps over stock photos.
