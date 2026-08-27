# maltsolutions.com

Static HTML rebuild of the MALT Solutions website, replacing the Squarespace-hosted original ahead of the **Sept 9, 2026** renewal. No build tools, no npm, no framework — plain HTML + one CSS file + local images. Deploys to Cloudflare Pages by pointing at the repo root.

---

## Local preview

Just open `index.html` in a browser. No build step, no server needed. All internal links are relative and work over `file://` and `https://`.

---

## Deploy to Cloudflare Pages

1. Push this repo to GitHub as `rksmith77/maltsolutions-site` (create the empty repo first at github.com, then `git remote add origin ...` and `git push -u origin main`).
2. In the Cloudflare dashboard: **Workers & Pages → Create → Pages → Connect to Git**.
3. Select the `rksmith77/maltsolutions-site` repo.
4. Build settings:
   - **Framework preset:** None
   - **Build command:** *(leave blank)*
   - **Build output directory:** `/`  (or leave blank — the root of the repo is the site)
   - **Root directory:** *(leave blank)*
5. Save and deploy. First deploy takes ~30 seconds. You'll get a `*.pages.dev` URL to preview before cutover.
6. Once you're happy, add the custom domain: **Pages project → Custom domains → Set up a custom domain → `maltsolutions.com`** (and repeat for `www.maltsolutions.com`). Cloudflare will give you the exact DNS records to add — save those for the next step.

---

## DNS cutover in Google Cloud DNS

Cloudflare Pages will show you the target records after you add the custom domain. Typical setup:

**CHANGE these records** (point them at Cloudflare):
- `A` for apex `maltsolutions.com` → Cloudflare Pages IP (Cloudflare gives you exact value)
- `CNAME` for `www.maltsolutions.com` → `<project>.pages.dev` (Cloudflare gives you exact value)

**LEAVE THESE ALONE** — do not touch anything email-related, or Google Workspace mail breaks:
- `MX` records (Google Workspace inbound mail)
- `TXT` record for domain verification (Google site verification)
- `TXT` for SPF (starts with `v=spf1`)
- `TXT` and `CNAME` for DKIM (subdomain like `google._domainkey`)
- `TXT` for DMARC (subdomain `_dmarc`, starts with `v=DMARC1`)

TTL: drop to 300 seconds a few hours **before** the cutover so propagation is fast if anything needs a rollback. Bump it back up after cutover is stable.

---

## Cancel Squarespace

**Don't cancel until the new site has been live and healthy for 2 weeks.** Squarespace remains a rollback path.

1. Log in to Squarespace → account settings.
2. **Billing → Subscriptions → Website plan → Cancel Subscription.**
3. Squarespace will offer to keep the site up until the paid period ends (Sept 9, 2026). Accept — the site stays live as a safety net until then, but no auto-renewal charges.
4. Also cancel any add-ons: Google Workspace-through-Squarespace (if applicable — R.K. manages Workspace directly), custom email plans, Acuity, etc. Verify none silently renew.
5. **Do NOT delete the Squarespace site itself for 2 weeks post-cutover.** Just cancel the subscription. Delete only after the new site has been stable and R.K. is confident.

---

## Content & fixes applied vs the Squarespace original

- Phone number corrected to `(303) 619-8339` (the old JSON-LD had `(303) 619-8336`)
- Instagram placeholder link removed everywhere (footer, JSON-LD `sameAs`)
- Favicon replaced (was Squarespace default; now a brand-blue "M" SVG at `favicon.svg`)
- Squarespace contact form replaced with a `mailto:info@maltsolutions.com` CTA
- Adobe Typekit replaced with Google Fonts (Inter)
- No analytics / tracking pixels
- Clean JSON-LD Organization + LocalBusiness schema on the homepage

## File tree

```
maltsolutions-site/
├── index.html              (home)
├── services.html
├── ai-workflows.html
├── about.html
├── booking.html
├── contact.html
├── css/style.css
├── images/                 (6 images: logo, hero photos, R.K. profile, booking graphic)
├── favicon.svg
├── robots.txt
├── sitemap.xml
├── README.md
└── .gitignore
```

## Editing later

- Copy changes: edit the `.html` file directly. Shared header/footer are duplicated across pages (no template engine); if you change nav links or footer, update all 6 files.
- Styling: single stylesheet at `css/style.css`.
- New page: copy an existing `.html` file, update title/meta/content, add an entry to `sitemap.xml` and the nav in all 6 files.
