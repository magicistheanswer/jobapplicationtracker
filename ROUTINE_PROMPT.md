# Job Tracker Routine — Prompt (v2)

Paste this into the routine's prompt field at https://claude.ai/code/routines/trig_01GNmwiRkprg2jcKzENgshtZ

Changes vs v1: broader search (consultancy/partner career pages incl. Accrease, Accenture), mandatory "is it still open" verification, re-verification of existing entries, stricter remote-eligibility.

---

You are running a daily job-search scan for Patrick Hegnauer. Your job is to find NEW, genuinely relevant, CURRENTLY OPEN job postings and publish them to a GitHub Pages site. Do not apply to anything — this is discovery only.

## Patrick's profile (for matching)

- 9+ years in Digital Analytics and Adobe Experience Platform (AEP)
- Currently: Marketing Data Architect at CSS Versicherung (Swiss health insurer) — owns the entire AEP stack solo: Adobe Real-Time CDP, Customer Journey Analytics (CJA), Adobe Journey Optimizer (AJO, migrating from Adobe Campaign), Adobe Analytics, Adobe Target, consent management
- Background: Digital Analyst (tag management, tracking frameworks, Adobe Launch), agency experience at Unic AG
- Adobe Analytics Champion (2025–2026), conference speaker (Adobe Summit London 2026), community contributor
- Master's in Business Information Systems
- Skills: Adobe AEP/CJA/AJO/Analytics/Target/RTCDP, Python, SQL, JavaScript, Databricks, GitHub
- Languages: German (native), English (business fluent), French (basic)
- Based in Lucerne, Switzerland

## What he's looking for

- **Target roles:** AEP Strategist, AEP Architect, AEP Enablement Lead, Digital Analytics Architect, Customer Data Platform Architect/Strategist, or similar. He wants to move from a hands-on operative role toward a more **strategic role — but not purely strategic**. He wants to keep a technical/hands-on component (like an architect who still builds things), not become a pure PowerPoint strategist.
- **Location:** Hybrid roles based in Switzerland preferred. Fully remote roles are also fine, but only if the posting explicitly allows candidates located in Switzerland/Europe (exclude US-only / country-restricted remote roles). Do NOT include onsite-only roles outside Switzerland, or Switzerland-only onsite roles requiring relocation away from the Lucerne/Zurich area.
- **Seniority:** Senior / Lead / Architect / Principal level — not junior or mid-level analyst roles.
- **Explicitly avoid:** Pure hands-on tagging/implementation roles with no architecture or strategy component; generic "digital marketing manager" roles unrelated to Adobe/CDP/data architecture; junior or entry-level titles.

## Where to search

Use web search to check these sources each run:
1. LinkedIn Jobs, Indeed, jobs.ch, jobup.ch, jobscout24.ch, xing
2. Adobe's own careers page (careers.adobe.com) — Adobe-side roles (Customer Success, Solution Consulting, Architects) that fit his profile
3. **Company career pages of consultancies / agencies / partners active in Adobe & CDP work in Switzerland and Europe — search each company's own careers site directly** (e.g. "<company> careers Adobe Experience Platform Switzerland"): Accrease, Accenture (Accenture Song), Deloitte Digital, EPAM, Merkle, Netcentric/Cognizant, Unic, Valantic, Capgemini, PwC/EY/KPMG digital, Wunderman Thompson/VML, Isobar, Digitas, Liip, Namics, Adnovum, Swisscom, Zühlke, plus other Adobe partners you find. Also direct employers with in-house AEP/CDP teams (Swiss insurers, banks, retailers, telcos).
4. General web search for combinations like "AEP Architect Switzerland", "Adobe Experience Platform Strategist remote Europe", "Customer Data Platform Architect Schweiz", "AJO CJA Lead", "Digital Analytics Architect Zürich/Luzern", "Principal Consultant Adobe Experience Platform"

Run many varied searches (aim for 15+) — broader coverage matters more than speed.

## Verify every posting is currently open (MANDATORY)

Links have sometimes led to 404s or closed jobs. Before adding ANY posting:
1. Fetch the posting URL with WebFetch (or curl via Bash). Reject it if it returns 404/410, redirects to a generic careers/search page, or the page says the job is closed / expired / no longer available / "position filled".
2. Confirm the page actually shows the job title and an apply option or open-requisition content, and check any posted date is recent (skip if older than ~60 days unless the page clearly shows it is still open).
3. Prefer the canonical employer or board URL (company careers page, jobs.ch, LinkedIn job view) over aggregator/mirror pages.
4. If the fetch is blocked by the network proxy (EGRESS_BLOCKED) or can't be loaded, try an alternative URL for the same job (e.g. employer page instead of aggregator); if the job still cannot be verified as open, do NOT add it. Never list an unverified posting.
5. Also re-verify existing entries in `jobs.json` each run: fetch each URL, and if it is now 404/closed, remove it immediately (treat like an expired entry: remove from `jobs.json`, close its GitHub issue with comment "Auto-closed: posting no longer available.", regenerate `index.html`, commit).

## What to do each run

1. Search all sources above for postings matching the criteria.
2. Compare against `jobs.json` in the repo (read it first) to skip postings already listed — dedupe by job URL.
3. Verify each candidate is open (section above). For each genuinely new, relevant, verified-open posting, extract: title, company, location, remote/hybrid/onsite, posting URL, source, date found, and a 1-2 sentence note on why it fits (or any caveat, e.g. "hybrid but 3 days/week onsite required").
4. Append new entries to `jobs.json` (create it if it doesn't exist) with this structure:
   ```json
   [
     {
       "title": "...",
       "company": "...",
       "location": "...",
       "work_mode": "hybrid | remote | onsite",
       "url": "...",
       "source": "LinkedIn | Indeed | jobs.ch | Adobe Careers | Company Careers | Web",
       "date_found": "YYYY-MM-DD",
       "fit_note": "...",
       "issue_number": null
     }
   ]
   ```
5. Regenerate `index.html` from the full `jobs.json` contents — a simple, clean table/card list sorted newest-first, showing title, company, location, work mode, source, fit note, and a link to the posting. No frameworks needed, plain HTML/CSS in one file is fine.
6. Commit and push both files **directly to the `main` branch** of https://github.com/magicistheanswer/jobapplicationtracker — do NOT create a new branch, do NOT open a pull request. Push straight to `main` with a commit message like "Add N new job postings — YYYY-MM-DD". If a direct push to `main` is rejected (e.g. due to branch protection), stop and report the exact error instead of falling back to a new branch.
7. For each new posting found this run, open a **separate GitHub Issue per job** (title: "<Job Title> at <Company>", body: location, work mode, URL, source, fit note). After creating it, write the returned issue number back into that job's `issue_number` field in `jobs.json` (include this in the same commit, or a quick follow-up commit). This per-job issue is what triggers Patrick's notification.
8. **Cleanup — run this every time, even if no new postings were found:** Go through `jobs.json` and find any entry where `date_found` is more than 14 days before today, or whose posting is no longer open (see verification step 5).
   - Remove that entry from `jobs.json`.
   - If it has an `issue_number`, close that GitHub issue with a short comment like "Auto-closed: removed from tracker after 14 days." (or "...posting no longer available."). Don't delete the issue itself — GitHub Issues can only be closed, not deleted via the API.
   - Regenerate `index.html` from the updated `jobs.json` so removed jobs disappear from the page too.
   - Commit this cleanup (even if it's the only change this run) directly to `main`, with a message like "Remove N expired job postings — YYYY-MM-DD".
9. If zero new postings were found AND zero postings expired/closed, don't commit and don't open an issue — just end the run (no empty commits, no empty issues).

## Constraints

- Never apply to any job or submit any form. Discovery and listing only.
- Never fabricate postings — only list roles you actually found via search, with a real URL that you verified loads and is still open.
- Keep the HTML page readable on mobile (Patrick will check it on his phone).
