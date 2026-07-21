# Stori Cohort Analytics

Browser-based cohort chart tool for the team. Upload or paste TSV/CSV, filter vintages, style the chart, and download PNGs for slides.

**No data is uploaded to a server** — everything runs in the browser on your machine.

## Use it

Production should be the Cloudflare Zero Trust hostname (for example `https://cohort-analytics.<your-zone>/`) once configured. See `ZERO_TRUST.md`.

The current GitHub Pages URL is public and should be treated as a fallback only:

```text
https://alan-solache.github.io/cohort-analytics-web/
```

1. Open the protected Cloudflare URL
2. Drop a TSV/CSV or paste from Excel
3. Toggle years, trim to max life, set Y range, edit the title name
4. Download or copy PNG

## Local preview

```bash
python3 -m http.server 8765
# open http://localhost:8765
```

## Deploy / update

This repo is a static site (`index.html`). Preferred production hosting is **Cloudflare Pages + Zero Trust** using `wrangler.toml` and `_headers` in this repo.

GitHub Pages still deploys on pushes to `main`, but it is public unless the repository/Pages settings are changed.

```bash
# after editing index.html
git add index.html
git commit -m "Update cohort analytics"
git push
```

### Custom domain

1. In the repo: **Settings → Pages → Custom domain** (or put your domain in `CNAME`)
2. At your DNS provider, add:
   - **Apex** (`example.com`): A records to GitHub Pages IPs  
     `185.199.108.153` `185.199.109.153` `185.199.110.153` `185.199.111.153`
   - **Or subdomain** (`charts.example.com`): CNAME → `YOUR_USER.github.io`

## Privacy note

The app is public (anyone with the link can open the UI). Your cohort files stay local unless someone pastes/uploads them in their own browser session.
