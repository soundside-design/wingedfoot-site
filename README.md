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

## Status

DNS cutover **done** 2026-08-30: the Squarespace Defaults preset was deleted and
the apex now has GitHub's four `A` records. The pages serve correctly over HTTP.

**HTTPS is pending GitHub's own DNS health check**, which lagged behind the old
records' 4-hour TTL. Check it with:

```bash
gh api repos/soundside-design/wingedfoot-site/pages/health --jq '{valid:.domain.is_valid, served:.domain.is_served_by_pages, reason:.domain.reason}'
```

Once `valid` is true, GitHub issues the certificate automatically; then enable
**Enforce HTTPS** (Settings → Pages, or `gh api -X PUT repos/soundside-design/wingedfoot-site/pages -f https_enforced=true`).
Do not hand Apple the privacy URL until HTTPS answers.

## DNS cutover, as executed (kept for reference / disaster recovery)

The pages are built and waiting; the apex still resolves to Squarespace's
parking page. In Squarespace → Domains → wingedfoot.ai → DNS Settings:

1. Delete the **Squarespace Defaults** preset (trash icon). It holds the four
   parking `A @` records, `CNAME www`, and — the one that actually matters — an
   `HTTPS @` record whose `ipv4hint` re-advertises the parking IPs. Leaving that
   behind makes browsers reach Squarespace even after the A records change.
2. Under **Custom records**, add four `A` records for `@`:

   ```
   185.199.108.153
   185.199.109.153
   185.199.110.153
   185.199.111.153
   ```

3. Optional: `CNAME www → soundside-design.github.io`.
4. Then, in this repo's Settings → Pages, tick **Enforce HTTPS** once GitHub has
   issued the certificate (a few minutes after DNS propagates).

**Do not touch the existing custom records** `A box`, `A turn` (both
`34.60.110.56`) or `CAA box` — those are the appliance's public edge and are
unrelated to this site. Do not add a CAA record at the apex; there is none
today, and one would inherit down to `box` and can break certificate renewal
there.

Verify afterwards:

```bash
dig +short wingedfoot.ai A            # expect the four 185.199.x addresses
curl -sI https://wingedfoot.ai/privacy | head -1
curl -sI https://box.wingedfoot.ai/ | head -1   # must still answer (404 is correct)
```

## Deliberately not here

`box.wingedfoot.ai` and `turn.wingedfoot.ai` are separate subdomain records
pointing at the appliance edge. They have nothing to do with this repo, and
changes here must never touch them.

© Soundside Design LLC
