# GTM Team Onboarding

An onboarding reference and hands-on workbook for teams learning Google Tag Manager. It is a single static page that reads as a scrolling guide or runs as a slide deck (press **P**, then use the arrow keys).

Live at https://gtm-onboarding.zarapalevani.com

## What's inside

- **Part I: the platform.** What Tag Manager does compared with the Google tag, server-side tagging and the Google tag gateway; the account → container → workspace → version → environment structure and permissions; the tools around it; how triggers, the data layer, consent mode, Google destinations and publishing work; and what changed in 2025 and 2026.
- **Part II: the workbook.** A glossary with a quiz mode, five common problems and their fixes, a 30-day checklist, and a five-task scenario lab for a fictional client (Harbor Lane Coffee).
- **Part III: supporting Tag Manager clients.** When a client needs server-side tagging or the tag gateway, recognizing common client issues, enterprise architecture, and costs, Tag Manager 360 and support.

Checklist ticks and lab answers are saved in the visitor's browser (`localStorage`, key `gtm-onboarding-v1`). The site also loads Google Tag Manager (`GTM-TTJX54`), which sends usage data to Google Analytics, and Cloudflare Web Analytics.

## Security headers and crawling

`_headers` sets the security headers Cloudflare sends with every page, including a Content-Security-Policy. It is the same policy as the GA4, SA360 and DV360 onboarding sites.

The page loads Google Tag Manager (`GTM-TTJX54`, the same container as the other onboarding sites), whose Custom HTML tags are added to the page inline. So `script-src` allows inline scripts (`'unsafe-inline'`) instead of listing them by hash. Don't add `sha256-` hashes back: when a hash is present, browsers ignore `'unsafe-inline'` and GTM's tags stop running.

The policy allows Google Tag Manager and Google Analytics (`*.googletagmanager.com`, `*.google-analytics.com`, `*.analytics.google.com`, `*.g.doubleclick.net`, `*.google.com`), and any `https:` image, because Google Signals uses country domains such as `google.ca`. **If a GTM tag starts loading a script or sending data to a new domain, add that domain to `script-src` or `connect-src` in `_headers` (on all the onboarding sites), or the browser will block it silently.** After a tag or header change, open the site and check the browser console for Content Security Policy errors.

The policy also allows Cloudflare Web Analytics, which Cloudflare adds to the page automatically: its script from `static.cloudflareinsights.com`, and its reports to this site's own `/cdn-cgi/rum` endpoint (`connect-src 'self'`) or to `cloudflareinsights.com`.

`robots.txt`, the robots `<meta>` tag and the `X-Robots-Tag` header ask search engines and AI crawlers not to index or use the site. They don't make it private: anyone with the link can still read it. `_redirects` sends `/README.md` to the home page, and `.assetsignore` keeps repo files such as this README and Cloudflare's generated `wrangler.jsonc` from being served. Anything else you add to the repo is public unless you list it there.

## Sources and accuracy

- Every technical claim was checked against Google's official Tag Manager Help Center pages, the Tag Manager release notes, Google's tag platform developer documentation and Cloudflare's Google tag gateway documentation on 23 November 2026. Each important claim links its source on the page, and the "Official documentation" slide lists them by topic.
- Tag Manager changed a lot in 2025–2026 (container optimization, the Google tag and Tag Manager unification, the tag gateway). Check the release notes before quoting how something works, and update the "checked on" dates when you re-verify.
- Where Google's pages disagree, the site says so. Example: server-side hosting costs are given as about $45 per server per month in the Cloud Run guide and about $50 in the learning course, with a minimum of two or three servers, so the site quotes a range.
- The lab is scenario-based: learners plan the tagging for a fictional client and compare with a model answer. Nothing is built or published. Consent defaults are the client's legal decision and are labeled as such.
- Not yet verified:
  - the "Judgment" note in Part III (neither server-side tagging nor the gateway is a way around consent or ad blockers, or worth it without someone to maintain it), which is our view, not Google's wording;
  - Tag Manager 360 pricing, which Google doesn't publish.
- The checklist refers to internal arrangements (an onboarding buddy, read access to clients' containers). Confirm these with the team before relying on them.
- Wording: the site says "client" for the companies the team supports. "Customer" appears only in the team name Customer Success.

## Editing

Edit `index.html` directly. All content, styles and scripts are in that one file.

The page was first generated from the DV360 site by `gtm-build/build.py` (in the parent folder, not in this repo), which swaps in `gtm-build/main.html` and GTM-specific names, storage keys and interactive data. Don't re-run it after editing `index.html`: it would overwrite those edits.

After a change, run `python -I gtm-build/qa_static.py <index> <js_out>`, `node --check <js_out>` and `python -I gtm-build/qa_links.py <index>` (the two `fonts.*` preconnect hosts always show as failing). Then test navigation, present mode (P, G, T, arrow keys, Esc), the interactive parts, saved progress after a reload, and the layout at 375 px wide.

## Deploying

There is no build step. Everything is in `index.html`, served as-is by a Cloudflare Worker with static assets, connected to this repo through Cloudflare's Git integration. A push to `main` deploys the site.

- Build command: *(empty)*
- Cloudflare generates a `wrangler.jsonc` that serves the repo root; `.assetsignore` keeps it off the site.
- Custom domain: `gtm-onboarding.zarapalevani.com`
