# The Sportscaster — thesportscasterfilm.com

Static one-page site for the documentary. No build step, no dependencies — push these
files to a repo and GitHub Pages serves them as-is.

```
index.html          the whole site (CSS is inline, one HTTP request)
404.html            branded not-found page
CNAME               custom domain for GitHub Pages
robots.txt          allows everything, points at the sitemap
sitemap.xml         single URL
favicon.ico         32/16px icon
.nojekyll           stops GitHub running Jekyll over the files
assets/fonts/       Charter Roman + Black, self-hosted as woff2
assets/img/         hero, band, trailer card, FanSided mark, icons, social card
```

Total repo is about 1.1 MB. First paint pulls roughly 345 KB (HTML + hero + two fonts).

---

## 1. Push to GitHub

```bash
cd the-sportscaster
git init
git add .
git commit -m "The Sportscaster — launch site"
git branch -M main
git remote add origin https://github.com/<you>/<repo>.git
git push -u origin main
```

Everything must sit at the **repo root** — `index.html` at the top level, not in a subfolder.

## 2. Turn on GitHub Pages

Repo → **Settings → Pages**

- Source: **Deploy from a branch**
- Branch: **main**, folder: **/ (root)**
- Custom domain: `thesportscasterfilm.com` — the `CNAME` file sets this, but confirm it
  appears in the box.

Add the domain in GitHub **before** pointing DNS at it. Doing it the other way round
briefly leaves the domain open to being claimed by someone else.

## 3. GoDaddy DNS

GoDaddy → your domain → **DNS → Manage Zones**.

**Delete first:** GoDaddy adds a parked A record on `@` and a `www` CNAME to
`_domainconnect` or a parking page. Both must go or they will fight these records.

**Add four A records** on `@`:

| Type | Name | Value           | TTL    |
|------|------|-----------------|--------|
| A    | @    | 185.199.108.153 | 1 hour |
| A    | @    | 185.199.109.153 | 1 hour |
| A    | @    | 185.199.110.153 | 1 hour |
| A    | @    | 185.199.111.153 | 1 hour |

**Add one CNAME** so `www` redirects to the apex:

| Type  | Name | Value              | TTL    |
|-------|------|--------------------|--------|
| CNAME | www  | `<you>.github.io.` | 1 hour |

Use your GitHub username — not the repo name.

Optional IPv6 (four AAAA records on `@`): `2606:50c0:8000::153`, `2606:50c0:8001::153`,
`2606:50c0:8002::153`, `2606:50c0:8003::153`.

## 4. HTTPS

DNS takes 10 minutes to a few hours to propagate. Once GitHub sees it, go back to
**Settings → Pages** and tick **Enforce HTTPS**. The certificate can take up to 24 hours
to become available — if the checkbox is greyed out, come back later. Don't launch
publicly until it's on.

Check DNS with:

```bash
dig thesportscasterfilm.com +short        # expect the four 185.199.x.153 addresses
dig www.thesportscasterfilm.com +short    # expect <you>.github.io
```

---

## Before you announce it

**The email form is not connected yet.** In `index.html`, find:

```html
<form class="follow-row" id="signup" action="SAILTHRU_ENDPOINT" method="post">
```

Replace `SAILTHRU_ENDPOINT` with the Sailthru form POST URL. The field is already named
`email`, which is what Sailthru expects. Until you replace it, submitting the form opens
the visitor's mail app addressed to press@fansided.com instead of failing silently — so
the page is safe to ship, but you're not building a list until it's wired.

**Still to add when you have them:**

- Trailer — the section shows the title card with a "coming soon" label and nothing
  clickable. When the trailer is public, that frame becomes the video embed.
- Texting signup — removed for launch because there was no Subtext number yet. It was a
  short block under the email form; easy to put back.
- Festival laurels — no row exists yet. Worth adding once the first selection lands.

## Editing

Everything is one file. The palette lives in the `:root` block at the top of the
`<style>`:

```css
--ink:#14161c;       /* page background  */
--paper:#f2f5f7;     /* primary text     */
--paper-dim:#9fb2bf; /* secondary text   */
--cream:#efe9e0;     /* quote block      */
--accent:#e83151;    /* red rules        */
```

The red is sampled from the trailer card; the greys come from the film's grading palette.

Images are referenced from `/assets/img/`. Swapping the hero is a one-line change — keep
it 16:9 and make sure faces sit in the upper third, since the title block occupies the
lower half.
