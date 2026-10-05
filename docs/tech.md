# Tech

## Website
- **Type:** static HTML, no build step. Files live in `site/`.
- **Host:** Netlify, project `sunshine-surveyors` (plan: nf_team_dev).
- **Live URL:** https://sunshinesurveyors.au (branch URL: main--sunshine-surveyors.netlify.app)
- **Deploys:** push to `main` on GitHub → Netlify auto-deploys. Pull requests get a deploy preview (confirmed working 2026-10-05).
- **Config:** `netlify.toml` sets publish folder `site/`, www → apex redirect, security headers.
- **Repo:** github.com/tommehhs/sunshine-surveyors-site. Public as of 2026-10-05; Tommy decided to make it private.

## Quote form
- Netlify Forms, form name `quote-request`.
- Fields: full_name, contact, site_address, service (checkboxes), services_summary, timeline, job_details, plus a `website` honeypot.
- Submits in place with JavaScript; `/thanks.html` is the fallback.
- **Where submissions go:** Netlify dashboard → Forms. Email notifications: unverified. Set them under Project configuration → Notifications → Emails and webhooks.

## Domains
| Domain | Use | Registrar | Paid / expiry |
|---|---|---|---|
| sunshinesurveyors.au | Website | Unverified (DNS on nameserver.net.au) | **Unpaid fees mentioned 2026-10-05; check** |
| sunshinesurveyors.com.au | Email (`info@`) | Unverified | Unverified |

If either domain lapses: the site or the email stops working, and the phone/email on the site become dead ends.

## Email
- `info@sunshinesurveyors.com.au`, hosted at email-hosting.net.au (from MX records). Whether it receives mail: unverified.
- Tommy may redo domains and email.

## Phone
- Public number: 0494 725 014 (Aldi Mobile prepaid SIM, Telstra network — from earlier planning; unverified that this is the same number).
- The prepaid must be kept active, or the number can be recycled.
- Planned: iPhone Live Voicemail now; Twilio call diversion and transcription later.

## Not built yet (from the July spec)
Next.js App Router, Tailwind, Supabase, Vercel. See `archive/build-spec-v1-summary.md`.
