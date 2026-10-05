# 0001 — This repo is the source of truth

**Date:** 2026-10-05
**Status:** Decided (Tommy)

## Context
Project knowledge was spread across Claude chats and got lost between sessions. The site files had already been moved into GitHub to stop them being lost.

## Decision
- The GitHub repo `sunshine-surveyors-site` holds both the website (`site/`) and the project knowledge (`docs/`).
- Netlify publishes only `site/`, so docs never go on the public website.
- The repo will be made private, because the docs contain business strategy.
- Every AI session reads `CLAUDE.md` and `docs/status.md` first, and updates them at the end.

## Why
Chats are disposable and hard to search. Files in the repo are versioned, readable by any Claude surface, and survive between sessions.
