# SendGrid Link Branding SSL Proxy — Livestockr

This is a minimal reverse proxy that forwards all traffic from your SendGrid
Link Branding subdomain (`url4469.livestockr.ca`) to SendGrid's servers
(`sendgrid.net`), while letting Vercel handle SSL for your custom domain.

This solves the "Your connection is not private" / NET::ERR_CERT_COMMON_NAME_INVALID
error, since SendGrid's default certificate doesn't cover your custom branded
subdomain, but Vercel will auto-issue and auto-renew a certificate for it.

This mirrors the same working setup built for HeirWise
(`url9641.heirwise.ca`), adapted for Livestockr's branded domain.

## Deploy Option A — via GitHub (recommended)

1. Clone this repo (already created at blu-geek/sendgrid-livestockr):
   ```
   git clone https://github.com/blu-geek/sendgrid-livestockr.git
   cd sendgrid-livestockr
   ```
2. Copy these files into the repo (api/proxy.js, vercel.json, package.json, README.md)
3. Commit and push:
   ```
   git add .
   git commit -m "Initial SendGrid link branding SSL proxy for Livestockr"
   git branch -M main
   git push -u origin main
   ```
4. Go to https://vercel.com/new and import the `sendgrid-livestockr` repo
5. Click Deploy — no build settings needed

## Deploy Option B — via Vercel CLI (no GitHub needed)

1. Install the CLI:
   ```
   npm install -g vercel
   ```
2. From inside this folder, run:
   ```
   vercel
   ```
3. Follow the prompts and accept defaults

## After deploying

1. In the Vercel dashboard, go to your project → Settings → Domains
2. Add `url4469.livestockr.ca`
3. Vercel will show a CNAME target, usually something like `xxxxxxxx.vercel-dns-017.com`
   or `cname.vercel-dns.com`
4. In GoDaddy DNS for livestockr.ca, edit the existing `url4469` CNAME record
   (currently pointing to SendGrid) to point to that Vercel target instead
5. Wait for DNS to propagate (check with: `dig CNAME url4469.livestockr.ca +short`)
6. Vercel will auto-issue an SSL certificate for the subdomain once DNS resolves
7. Test by visiting a real tracked link from a Livestockr SendGrid email —
   it should load without a certificate warning, and correctly redirect
   (not show a SendGrid "Wrong Link" error)
8. Contact SendGrid support to enable "SSL for Click and Open Tracking" on
   the Livestockr sending account now that the proxy is confirmed working

## Notes

- The branded subdomain is set in `YOUR_DOMAIN` inside `api/proxy.js`
  (currently `url4469.livestockr.ca`) — update if SendGrid issues a
  different link branding hostname later.
- `servername: SENDGRID_HOST` is required — without it, Node validates
  the TLS certificate against the Host header instead of the actual
  connection target, causing a "Hostname/IP does not match certificate's
  altnames" error.
- This does not touch or migrate main `livestockr.ca` DNS — only the one
  `url4469` CNAME record changes.
- No Cloudflare, Hetzner, or other infrastructure required — Vercel handles
  both hosting of the proxy and SSL issuance.
