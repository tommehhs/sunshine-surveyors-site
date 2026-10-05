# CLAUDE.md — AI working brief

This repo is the **single source of truth** for Sunshine Surveyors: the website *and* the project knowledge.
Chats are disposable; this folder is not. If something important is decided or learned in a conversation, it gets written here.

## Read order (start of every session)
1. `docs/status.md` — where things stand, open questions, next actions. **Always read first.**
2. The one topic file the task needs (see map below). Don't load everything.
3. `docs/decisions/` — check before re-opening a question that may already be decided.

## File map
| File | What lives there |
|---|---|
| `docs/status.md` | Current state, open questions, next 3 actions. Living document. |
| `docs/business.md` | Entity, brand, services offered/not offered, compliance wording, pricing model |
| `docs/roadmap.md` | Direction options, phases, in/out of scope |
| `docs/tech.md` | Hosting, deploy pipeline, forms, domains, email, phone, accounts |
| `docs/marketing.md` | Google Business Profile, SEO, keywords, target suburbs |
| `docs/people.md` | Partners, prospects, contacts (business info only) |
| `docs/decisions/NNNN-*.md` | One file per decision: date, context, decision, why |
| `docs/research/` | Competitor and market notes |
| `docs/archive/` | Superseded material kept for reference (incl. full July build spec) |
| `docs/sources.md` | Which chats and accounts the knowledge came from |
| `CHANGELOG.md` | One line per session: date — what changed |
| `site/` | The live website. **Only this folder is published** (see `netlify.toml`). |

## Rules
- **Never put notes, docs or secrets in `site/`** — everything in it is public on the internet.
- **No secrets anywhere in the repo**: no passwords, API keys, account numbers. Name the account/where it lives instead ("Netlify login: Tommy's Google").
- **Mark certainty.** Facts Tommy confirmed are plain statements. Anything unverified gets `(unverified)`. Your own suggestions go under "Options" or "Suggestions", never written as decisions.
- **Decisions are Tommy's.** Only write a `decisions/` file after he chooses. Status of an undecided question = "Open".
- **Keep files short and single-topic.** If a file passes ~150 lines, split it.
- **Date things** as `YYYY-MM-DD` (Melbourne time).

## End of every session
1. Update `docs/status.md` (state, open questions, next actions, "Last updated").
2. Add a line to `CHANGELOG.md`.
3. Write a `decisions/` file for anything Tommy decided.
4. Commit with a plain message describing what changed.

## Compliance — non-negotiable
Sunshine Surveyors does **not** perform cadastral (boundary/title) surveys. In Victoria these require a Licensed Surveyor registered with the Surveyors Registration Board of Victoria. Any site copy that mentions boundary/title work must say it is referred to or performed by a licensed surveyor. Keep the footer compliance line on every page.

## Working on the site
- Plain static HTML, no build step. Edit files in `site/` directly.
- Deploys: push to `main` → Netlify auto-deploys to https://sunshinesurveyors.au. Pull requests get a Netlify deploy preview link.
- Prefer a branch + pull request so Tommy can check the preview before it goes live.
- The quote form is a Netlify Form (`name="quote-request"`). Fields must exist in the static HTML for Netlify to detect them.
