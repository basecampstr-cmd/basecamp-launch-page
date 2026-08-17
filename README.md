# Basecamp Getaways — Launch Package page

Static one-page site for the $5,000 Basecamp Launch Package, served at
**https://launch.basecampgetaways.com**

This page is gated: owners should only reach it after submitting the income
projection form on the owners landing page. It is set to `noindex` so it will
not show up in search results — see [Why noindex](#why-noindex) below.

---

## Contents

```
.
├── index.html                                   the page (self-contained, no build step)
├── 404.html                                     branded not-found page
├── CNAME                                        custom domain for GitHub Pages
├── robots.txt                                   blocks crawlers (gated page)
├── basecamp-launch-package-terms.pdf            18-page program guide (the download)
├── og-launch-package.png                        1200×630 social preview
└── favicon.ico / favicon-32 / -180 / -512       icons
```

No dependencies, no build, no framework. The only external request the page
makes is to Google Fonts.

---

## Deploy in five steps

### 1. Create the repo

On GitHub, create a new **public** repository — for example
`basecamp-launch-page`. Public matters: GitHub Pages requires a public repo
unless the account is on a paid plan.

### 2. Push these files to the repo root

`index.html` must sit at the **root** of the repo, not in a subfolder.

```bash
git remote add origin https://github.com/<owner>/<repo>.git
git branch -M main
git push -u origin main
```

No terminal? Open the repo on GitHub → **Add file → Upload files** → drag
everything in this folder (including the `assets` folder) → **Commit changes**.
Uploading via the web UI works exactly the same.

### 3. Turn on GitHub Pages

Repo → **Settings → Pages**

| Setting | Value |
| --- | --- |
| Source | Deploy from a branch |
| Branch | `main` |
| Folder | `/ (root)` |
| Custom domain | `launch.basecampgetaways.com` |
| Enforce HTTPS | ✅ (tick once the certificate is issued) |

The `CNAME` file in this repo already contains the custom domain, so the field
should populate itself when Pages first builds.

### 4. Add the DNS record

At whoever manages DNS for `basecampgetaways.com`:

| Field | Value |
| --- | --- |
| Type | `CNAME` |
| Name / Host | `launch` |
| Value / Target | `<owner>.github.io` |
| TTL | default (or 300) |

Replace `<owner>` with the GitHub account or organization name that owns the
repo — for example `josephkirton.github.io`. The trailing dot may be required
depending on the DNS provider. **Do not** point this at an IP address, and do
not touch the existing records for the apex domain or `www` — the main site on
AWS is unaffected by this.

### 5. Wait, then check

DNS usually propagates in 5–30 minutes; the HTTPS certificate can take up to
an hour after that. Then confirm:

- `https://launch.basecampgetaways.com` loads the page
- **Enforce HTTPS** is ticked in Settings → Pages
- The **Download Terms & Conditions** button downloads the PDF
- `https://launch.basecampgetaways.com/basecamp-launch-package-terms.pdf`
  opens directly — this is the URL to link from emails and GoHighLevel

---

## Editing the page

### Point owners at the terms PDF from email

Link straight to:

```
https://launch.basecampgetaways.com/basecamp-launch-package-terms.pdf
```

### Replace the terms PDF

Drop the new file at `basecamp-launch-package-terms.pdf`, keeping the
same filename, and commit. Nothing in `index.html` needs to change.

If you rename it, update this line near the bottom of `index.html`:

```js
var PDF_URL = "basecamp-launch-package-terms.pdf";
```

### Change copy

Everything is plain HTML in `index.html`. The sections in order are:

| Anchor | Section |
| --- | --- |
| `#top` | hero |
| `#packages` | four package cards + comparison table |
| `#qualify` | $85,000 projected standard, Owner Distribution note |
| — | the three commitments, Exhibit B peak dates |
| `#how` | Performance Period definition, Package 04 vesting |
| `#faq` | FAQ |
| `#next` | what happens next, terms download |

Brand colours are CSS variables at the top of the `<style>` block:

```css
--navy:#28404F;  --navy-deep:#1B2E39;  --gold:#C9A63D;
--gold-dk:#8F7320;  --cream:#FDFAF1;  --sand:#F5EEDA;
```

### Update the social preview

Replace `og-launch-package.png` (1200×630). The `og:image` URLs in the
`<head>` are absolute, so they only need changing if the domain changes.

---

## Why noindex

The page assumes the reader has already submitted the income projection form —
the hero opens with "Thanks, your projection is on its way." If Google indexed
it, cold visitors would land on a thank-you page for something they never did,
and the gate on your owners landing page would leak.

Two things enforce this: `<meta name="robots" content="noindex, nofollow">` in
`index.html` and `Disallow: /` in `robots.txt`.

If you later want this page to be publicly discoverable, remove both, and
rewrite the hero and the `#next` section for a visitor who has not submitted
anything.

---

## Notes

- **The apex domain is untouched.** `basecampgetaways.com` stays on its current
  AWS host. This adds one subdomain and nothing else.
- **`basecampgetaways.com/launch` is not possible from GitHub Pages** while the
  apex points at AWS — a path is served by whatever host answers the apex. To
  use that exact URL, the file would need to go on the AWS host instead.
- **Nothing on this page collects data.** There is no form and no tracking
  script. Lead capture stays on the owners landing page ahead of this one.
- **The management agreement controls.** This page and the PDF are summaries of
  the program; the agreement and its exhibits contain the binding terms.
