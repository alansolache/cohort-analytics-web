# Stori Cohort Analytics

Browser-based cohort chart tool for the team. Upload or paste TSV/CSV, filter vintages, style the chart, and download PNGs for slides.

**No data is uploaded to a server** — everything runs in the browser on your machine.

## Use it

1. Open the site (GitHub Pages URL or your custom domain)
2. Drop a TSV/CSV or paste from Excel
3. Toggle years, trim to max life, set Y range, edit the title name
4. Download or copy PNG

## Local preview

```bash
python3 -m http.server 8765
# open http://localhost:8765
```

## Deploy / update

This repo is a static site (`index.html`). Pushing to `main` publishes via GitHub Pages.

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
