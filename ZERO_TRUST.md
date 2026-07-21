# Cloudflare Zero Trust (team-only access)

GitHub Pages is public by default. Put **Cloudflare Zero Trust Access** in front of your **custom domain** so only teammates can open the app.

## Recommended setup

1. Add your domain to Cloudflare (DNS).
2. Create a DNS record for the chart app, e.g. `charts.yourdomain.com`:
   - Type: **CNAME**
   - Target: `alan-solache.github.io`
   - Proxy status: **Proxied** (orange cloud) — required for Access
3. In GitHub → repo **Settings → Pages → Custom domain**, set `charts.yourdomain.com` and wait for HTTPS.
4. In Cloudflare Zero Trust dashboard:
   - **Access → Applications → Add an application → Self-hosted**
   - Application domain: `charts.yourdomain.com`
   - Policy example:
     - Action: **Allow**
     - Include: **Emails ending in** `@storicard.com`  
       (or a specific email list / Google Workspace IdP)
5. Save. Visiting the domain should show a Cloudflare login/OTP gate before the chart UI.

## Important

- Share only the **custom domain** with teammates (behind Zero Trust).
- The raw GitHub Pages URL (`https://alan-solache.github.io/cohort-analytics-web/`) is still public unless you also protect it or avoid sharing it.
- Chart data still never uploads to a server — Access only gates who can open the page.

## Optional: Cloudflare Pages instead of GitHub Pages

If you prefer hosting on Cloudflare:

```bash
npx wrangler pages project create cohort-analytics-web
npx wrangler pages deploy . --project-name=cohort-analytics-web
```

Then attach the custom domain in Cloudflare Pages and create the same Access application on that hostname.
