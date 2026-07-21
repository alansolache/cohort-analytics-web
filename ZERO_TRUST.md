# Cloudflare Zero Trust deployment

GitHub Pages is public by default. For this tool, the safer production pattern is:

```text
Cloudflare Pages static site
→ private subdomain
→ Cloudflare Zero Trust Access policy
```

The app still runs entirely in the browser — uploaded/pasted cohort data is not sent to a server by this code — but Access gates who can load the UI.

## Recommended production hostname

Use a dedicated hostname similar to the CI Deck, for example:

```text
cohort-analytics.<your Cloudflare zone>
```

Examples once you confirm the zone you want to use:

- `cohort-analytics.solachealan.com`
- `cohorts.solachealan.com`
- another subdomain under the same Cloudflare-managed zone as the CI Deck

## Option A — Cloudflare Pages + Zero Trust (recommended)

1. In Cloudflare, create/import the Pages project:
   - **Workers & Pages → Create → Pages → Connect to Git**
   - GitHub repo: `alan-solache/cohort-analytics-web`
   - Production branch: `main`
   - Build command: leave blank
   - Build output directory: `/`

2. Add the custom domain:
   - **Workers & Pages → cohort-analytics-web → Custom domains → Set up a custom domain**
   - Domain: `cohort-analytics.<your zone>`
   - Cloudflare will create the DNS record automatically.

3. Add Cloudflare Zero Trust Access:
   - **Zero Trust → Access → Applications → Add an application → Self-hosted**
   - Application name: `Stori Cohort Analytics`
   - Application domain: `cohort-analytics.<your zone>`
   - Session duration: `8h` or `24h`
   - Policy: `Allow approved users`
   - Include one of:
     - Specific approved emails, or
     - Emails ending in `@storicard.com`, or
     - Your Google Workspace / IdP group

4. Verify in an unauthenticated/private browser window:
   - `https://cohort-analytics.<your zone>/` should show the Cloudflare Access login first.
   - After login, the chart UI should load.

## Option B — Keep GitHub Pages behind Cloudflare Access

This is workable, but weaker because the raw GitHub Pages URL remains public:

1. Set GitHub Pages custom domain for this repo to `cohort-analytics.<your zone>`.
2. In Cloudflare DNS, create a **proxied** CNAME:
   - Name: `cohort-analytics`
   - Target: `alan-solache.github.io`
   - Proxy status: **Proxied** / orange cloud
3. Add the same Zero Trust Access self-hosted app on that hostname.
4. Share only the custom hostname, not `https://alan-solache.github.io/cohort-analytics-web/`.

## Security headers included in this repo

This repo includes a Cloudflare Pages `_headers` file with:

- Content Security Policy restricting scripts/styles/fonts/images to required origins only.
- `X-Frame-Options: DENY` and `frame-ancestors 'none'` to prevent clickjacking.
- `Referrer-Policy: no-referrer`.
- `X-Content-Type-Options: nosniff`.
- Restrictive `Permissions-Policy` disabling camera/microphone/geolocation/payment/USB/etc.
- `Cache-Control: no-store` for HTML.

`index.html` also includes a meta CSP/referrer/robots policy as a fallback for GitHub Pages, but the HTTP headers from Cloudflare Pages are stronger.

## What Hermes needs from Alan to finish the setup

To actually deploy/protect this for you, send/confirm:

1. **Subdomain choice** — e.g. `cohort-analytics.solachealan.com` or another domain under your Cloudflare account.
2. **Who should be allowed** — specific emails, `@storicard.com`, a Google Workspace group, or owner-only.
3. **Cloudflare access path** — either:
   - authorize Wrangler locally / provide a scoped Cloudflare API token via your secure secret store, or
   - do the Cloudflare dashboard steps above and I can verify the resulting URL.
4. Whether you want the GitHub repo to remain **public** or be changed to **private**. Recommended: private if this is for internal team tooling.
