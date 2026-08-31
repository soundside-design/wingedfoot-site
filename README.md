# wingedfoot-site

The public pages for **wingedfoot.ai** — a landing page plus the `/privacy` and
`/support` pages Apple requires for App Store and external TestFlight review.

Static HTML, no build step, served by GitHub Pages from `main`. The `CNAME` file
binds the apex domain; DNS is managed at Squarespace (the registrar), where the
apex `A` records point at GitHub Pages.

## Source of truth for the wording

The prose originates in the private Wingedfoot repo at `appstore/site/`
(`privacy-policy.md`, `support.md`), where it is reviewed alongside the app.
**Change the wording there first, then port it here** so the published pages and
the reviewed drafts do not drift apart.

Two substitutions are made when porting: the effective date is set to the
publication date, and the contact placeholder becomes `info@soundsidedesign.com`.

## Deliberately not here

`box.wingedfoot.ai` and `turn.wingedfoot.ai` are separate subdomain records
pointing at the appliance edge. They have nothing to do with this repo, and
changes here must never touch them.

© Soundside Design LLC
