# kovatec.fi

The Kovatec website, <https://kovatec.fi>. It is one static page: HTML and CSS, no JavaScript, no build step. GitHub Pages serves it from the `docs/` folder of the `main` branch of this repository.

## Change the page

You need `git` and Python 3. On macOS both come with the Xcode Command Line Tools (`xcode-select --install`).

1. **Get the code** (once). In Terminal:

   ```bash
   git clone https://github.com/Kovatec-fi/kovatec.fi.git ~/Documents/kovatec.fi
   ```

   If the folder already exists, update it instead: `cd ~/Documents/kovatec.fi && git pull`.

2. **Edit.** Text is in `docs/index.html`, layout and colours in `docs/styles.css`. Do not add `style="…"` attributes or `<script>` tags; the page's security policy blocks them (see [Security](#security)). Put new styles in `docs/styles.css`.

3. **Preview on your Mac.** In Terminal:

   ```bash
   cd ~/Documents/kovatec.fi
   python3 -m http.server 8000 --directory docs
   ```

   Open <http://localhost:8000> in a browser. Leave Terminal running while you look; press Ctrl+C there to stop it.

4. **Tell search engines it changed.** If visible content changed, set `<lastmod>` in `docs/sitemap.xml` to today's date (`YYYY-MM-DD`).

5. **Publish.** In the same folder:

   ```bash
   git add -A
   git commit -m "feat(site): describe the change"
   git push
   ```

   GitHub Pages publishes the push automatically. Watch the **Actions** tab of the repository: the run called "pages build and deployment" turns green when the new version is live. Then reload <https://kovatec.fi>.

## Security

- **Content Security Policy** (a `<meta>` tag at the top of `docs/index.html` and `docs/404.html`): the browser loads only this site's own styles, fonts and images, runs no scripts, and refuses forms and `<base>` tags. Anything injected into the page cannot run.
- **No third parties.** Fonts are self-hosted in `docs/assets/fonts/`; there are no analytics, trackers or CDNs. Adding one means editing the policy on purpose.
- **HTTPS only.** GitHub Pages holds the certificate for kovatec.fi and redirects `http://` to `https://`.
- **Domain verification for the Kovatec-fi organisation** on GitHub (TXT record `_github-pages-challenge-Kovatec-fi`, see [Domain and DNS](#domain-and-dns)). Once GitHub confirms it under organisation **Settings → Pages → Verified domains**, no other GitHub account can publish a site under kovatec.fi.
- **CAA record**: only Let's Encrypt, which GitHub Pages uses, may issue certificates for kovatec.fi.
- **Security contact** for researchers: `docs/.well-known/security.txt` (renew the `Expires` date yearly).
- Everyone with write access to this repository can change the public website. Keep two-factor authentication on for those GitHub accounts.

## Search engines

- `<head>` of `docs/index.html` holds the title, description, canonical URL, Open Graph tags (link previews) and the company's structured data (JSON-LD: name, legal name, address, VAT, LEI, contact). Keep that data identical to the page footer and the Google Business Profile.
- `docs/robots.txt` allows all crawlers and points to `docs/sitemap.xml`.
- `docs/assets/og-image.png` (1200×630) is the picture shown when the link is shared.
- Google Search Console has kovatec.fi as a Domain property under timotej@kovacic.pro. After a content change, open **URL inspection**, enter `https://kovatec.fi/` and click **Request indexing**.

## Files

| Path | What it is |
|---|---|
| `docs/index.html` | The page |
| `docs/styles.css` | All styles |
| `docs/404.html` | Page shown for unknown addresses |
| `docs/assets/` | Logo, favicons, sharing image, fonts (Chakra Petch, Space Grotesk; SIL Open Font License) |
| `docs/CNAME` | Tells GitHub Pages the site's domain is `kovatec.fi` |
| `docs/.nojekyll` | Serves files as they are, without GitHub's Jekyll processing |
| `docs/robots.txt`, `docs/sitemap.xml` | For search engines |
| `docs/.well-known/security.txt` | Security contact |
| `VERSION`, `CHANGELOG.md` | Release number and history |

## Domain and DNS

kovatec.fi is registered at Domainhotelli; its DNS is edited in Domainhotelli's cPanel, under **Zone Editor**. The website needs these records:

| Name | Type | Value |
|---|---|---|
| `kovatec.fi` | A | `185.199.108.153`, `185.199.109.153`, `185.199.110.153`, `185.199.111.153` |
| `kovatec.fi` | AAAA | `2606:50c0:8000::153`, `2606:50c0:8001::153`, `2606:50c0:8002::153`, `2606:50c0:8003::153` |
| `www.kovatec.fi` | CNAME | `kovatec-fi.github.io` |
| `kovatec.fi` | CAA | `0 issue "letsencrypt.org"` |
| `_github-pages-challenge-Kovatec-fi.kovatec.fi` | TXT | GitHub's code for verifying the domain; keep it |

GitHub Pages issues and renews the HTTPS certificate itself. If the DNS records above ever change and the repository's **Settings → Pages** shows "Enforce HTTPS — Unavailable", remove the custom domain there, save, add `kovatec.fi` again and save; GitHub then requests a new certificate. GitHub records both steps as commits ("Delete CNAME", "Create CNAME") on `main`.

Email for kovatec.fi runs on Google Workspace. Do not change the MX, SPF (`v=spf1 …`), `google._domainkey` or `_dmarc` records when working on the website. Keep the `crtpjmdtgmnj` CNAME as well: Google Workspace and Search Console use it to verify that you own the domain.

## Releases

Each release is a code commit followed by a `chore(release): X.Y.Z` commit that changes only `VERSION` and `CHANGELOG.md`, an annotated tag `vX.Y.Z` and a GitHub release titled `kovatec.fi X.Y.Z`.
