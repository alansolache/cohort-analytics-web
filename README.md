1|# Stori Cohort Analytics
2|
3|Browser-based cohort chart tool for the team. Upload or paste TSV/CSV, filter vintages, style the chart, and download PNGs for slides.
4|
5|**No data is uploaded to a server** — everything runs in the browser on your machine.
6|
7|## Use it
8|
9|Production should be the Cloudflare Zero Trust hostname (for example `https://cohort-analytics.<your-zone>/`) once configured. See `ZERO_TRUST.md`.
10|
11|The current GitHub Pages URL is public and should be treated as a fallback only:
12|
13|```text
14|https://alan-solache.github.io/cohort-analytics-web/
15|```
16|
17|1. Open the protected Cloudflare URL
18|2. Drop a TSV/CSV or paste from Excel
19|3. Toggle years, trim to max life, set Y range, edit the title name
20|4. Download or copy PNG
21|
22|## Local preview
23|
24|```bash
25|python3 -m http.server 8765
26|# open http://localhost:8765
27|```
28|
29|## Deploy / update

`main` is the single source of truth. `https://cohorts.koai.app` is the only canonical production URL.

Cursor, Hermes, and other tools must create a branch and merge changes into `main`. GitHub Actions deploys every update to `main` into the existing Cloudflare Pages project `cohort-analytics-web`. Do not deploy uncommitted local files directly to production.

```bash
git checkout main
git pull origin main
git checkout -b update/short-description
# make changes
git add .
git commit -m "Describe the update"
git push -u origin update/short-description
```

Open and merge a pull request. Production updates automatically after the merge.

## Privacy note
51|
52|The app is public (anyone with the link can open the UI). Your cohort files stay local unless someone pastes/uploads them in their own browser session.
53|