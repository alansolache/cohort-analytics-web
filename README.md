# Stori Cohort Analytics

Browser-based cohort chart tool for the team. Upload or paste TSV/CSV, filter vintages, style the chart, and download PNGs for slides.

**No data is uploaded to a server** — everything runs in the browser on your machine.

## URLs

| Role | URL | Access |
| --- | --- | --- |
| **Production** | https://cohorts.koai.app/ | Cloudflare Zero Trust |
| **Sandbox** | https://alansolache.github.io/cohort-analytics-web/ | Public GitHub Pages |

> First-time enable (needs repo **Admin** once):  
> GitHub → **Settings → Pages → Build and deployment → Source: GitHub Actions**  
> Then re-run the workflow **Deploy GitHub Pages sandbox**, or push any commit to `main`.

Use production with the team. Use the GitHub Pages sandbox for quick public checks without Access login.

## Use it

1. Open production (or the sandbox)
2. Drop a TSV/CSV or paste from Excel
3. Toggle years, trim to max life, set Y range, edit the title name
4. Download or copy PNG

## Local preview

```bash
python3 -m http.server 8765
# open http://localhost:8765
```

## Deploy / update

`main` is the single source of truth.

- Every push/merge to `main` deploys **production** to Cloudflare Pages (`cohort-analytics-web`) via GitHub Actions.
- The same push also refreshes the **GitHub Pages sandbox**.

Cursor, Hermes, and other tools should create a branch and merge into `main`. Do not deploy uncommitted local files directly to production.

```bash
git checkout main
git pull origin main
git checkout -b update/short-description
# make changes
git add .
git commit -m "Describe the update"
git push -u origin update/short-description
```

Open and merge a pull request. Production and sandbox update after the merge.

See `ZERO_TRUST.md` for Cloudflare Access setup.
