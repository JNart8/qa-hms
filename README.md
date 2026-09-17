# QA Runbook — IBS Haulage Management System

This folder holds a copy of `qa-runbook.html` — the manual QA runbook for
the `ibs-haulage-management-system` Laravel app — tracked separately so
testers can pull it down and run it without needing a checkout of the
application codebase itself. It's a snapshot, not a live sync; see
[Relationship to the app repo](#relationship-to-the-app-repo) below for
how to keep it current.

## What this is

`qa-runbook.html` is a single self-contained HTML file — no build step, no
server required. Open it directly in a browser (double-click, or drag it
into a tab) to get an interactive, dependency-ordered manual test script
for the whole application.

It's not documentation to read top to bottom — it's a tool you check things
off in while you click through the real app in another window/tab.

## What it covers

120 test cases across 21 phases (0–20), ordered so that each phase only
depends on data created by an earlier one (e.g. Locations and Service
Types before Trips, Trips before Invoicing, Invoicing before Payments):

| # | Phase |
|---|-------|
| 0 | Environment sanity (fresh install, seed data present) |
| 1 | Staff auth & session security (login, forced password change, lockout, concurrent-session guard, idle timeout) |
| 2 | Settings (org profile, logo, default currency) |
| 3 | Staff users (create/edit/deactivate/reset password, self-deactivation guard) |
| 4 | Staff profile (self-service contact/password change) |
| 5 | Locations |
| 6 | Service types |
| 7 | Fleet: trucks |
| 8 | Fleet: drivers |
| 9 | Accounts & cash book |
| 10 | Expense categories |
| 11 | Clients & portal users |
| 12 | Transport requests & quotes |
| 13 | Trips |
| 14 | Invoicing |
| 15 | Payments |
| 16 | Reports |
| 17 | Referential-integrity delete guards |
| 18 | Audit log |
| 19 | Client portal |
| 20 | Cross-role authorization matrix (admin/finance/operations boundaries) |

Each test case has: the manual steps to follow, the expected result, a
pass/fail/pending status toggle, and a free-text notes field. A "Test data
reference" panel at the top lists the exact names/codes to reuse across
phases (staff accounts, locations, trucks, drivers, sample clients and
trips) so later phases line up with what earlier phases created.

## Solo use (no setup)

Just open the file. Progress is saved to that browser's `localStorage`
only — it won't be visible to anyone else and won't sync across devices.
This is the default/fallback mode.

## Shared/live use (a team testing together)

The page can optionally sync pass/fail/notes in real time across everyone
running it, via a small [Supabase](https://supabase.com) backend:

1. Create a free Supabase project.
2. Run the SQL shown in the on-page setup banner (also embedded in the
   file as `SETUP_SQL`) to create the `qa_results` table with permissive
   row-level-security policies and enable realtime on it.
3. Copy the project's URL and `anon` public key into the
   `SUPABASE_URL` / `SUPABASE_ANON_KEY` constants near the top of the
   `<script>` block at the bottom of the file.
4. Re-share the edited file with the team — the top bar shows a live
   connection status pill, a shared pass/total counter, and a "Reset all
   (everyone)" button once connected.

As of this writing, the copy in the app repo (`docs/qa-runbook.html`)
already has a project's URL/anon key filled in (not the placeholder
state), so out of the box it should connect straight to that shared
backend. The `anon` key is meant to be public/client-side — but note the
RLS policies on `qa_results` allow anyone with that key to read, insert,
update, and delete rows, so treat the key as "shareable with testers," not
secret, and don't point it at a Supabase project used for anything else.

## Relationship to the app repo

The canonical copy of this file lives at `docs/qa-runbook.html` in the
`ibs-haulage-management-system` repo. Treat that as the source of truth
when the test plan changes (new modules, changed workflows); this repo
exists so the runbook can be handed to QA/testers as a standalone
artifact without giving them the application source.
