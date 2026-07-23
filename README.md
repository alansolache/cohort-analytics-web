# Stori Cohort Analytics

Browser-based cohort chart tool for the team. Upload or paste TSV/CSV, filter vintages, style the chart, and download PNGs for slides.

**No data is uploaded to a server** — everything runs in the browser on your machine.

## URLs

| Role | URL | Notes |
| --- | --- | --- |
| **Production** | https://cohorts.koai.app/ | Cloudflare Zero Trust (team login) |
| **Sandbox** | https://cohort-analytics-web.pages.dev/ | Same deploy, public, no login |

Both update automatically on every push/merge to `main`. No GitHub Pages setup needed.

## Use it

1. Open production or the sandbox
2. Drop a TSV/CSV or paste from Excel
3. Toggle years, trim to max life, set Y range, edit the title name
4. Download or copy PNG

## Local preview

```bash
python3 -m http.server 8765
# open http://localhost:8765
```

## Deploy / update

`main` is the single source of truth. GitHub Actions deploys to Cloudflare Pages project `cohort-analytics-web`.

```bash
git checkout main
git pull origin main
git checkout -b update/short-description
# make changes
git add .
git commit -m "Describe the update"
git push -u origin update/short-description
```

Open and merge a pull request. Production (`cohorts.koai.app`) and sandbox (`.pages.dev`) update after the merge.

See `ZERO_TRUST.md` for Cloudflare Access setup on the production hostname.
