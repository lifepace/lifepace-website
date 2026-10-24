# lifepace.eu

The public website: a landing page, the privacy policy and the imprint.
Plain HTML and one stylesheet, no build step, no cookies, no external
requests.

```text
index.html               landing page
privacy.html             privacy policy (app, planned Garmin connection, website)
imprint.html             Impressum (§ 5 DDG)
404.html                 "page not found", served by GitHub Pages for unknown paths
style.css                the app's palette: navy #0E1829, mint #5EE0B8
icon.svg                 the app icon, from lifepace-ios ios/design/AppIcon.svg
favicon.ico              16, 32 and 48 px, rounded
apple-touch-icon.png     180 px, square and opaque (iOS rounds it)
icon-192.png, icon-512.png   rounded, for the web manifest
icon-maskable-512.png    full bleed, for Android's own shapes
og-image.png             1200 x 630 preview for links shared in chats and social media
site.webmanifest         name, colours and icons for "Add to Home Screen"
robots.txt, sitemap.xml  for search engines
CNAME                    lifepace.eu (ignored by the Actions deployment; see below)
```

The PNG icons are cut from the app's own
`Assets.xcassets/AppIcon.appiconset/AppIcon-1024.png`; regenerate them when
the app icon changes. Pages link to `/...` from the site root, so open them
through a local server (`python3 -m http.server`), not as files.

## Before it goes live

- Fill in every `[FILL IN]` in `privacy.html` and `imprint.html`: name,
  address and a working email address. Germany requires the imprint for
  a site run from there.
- Have the privacy policy reviewed. It describes the app as it is (all
  data on the iPhone) and the Garmin connection as planned (Tree M
  decisions M3, M4 in the engine's docs/review-2026-10-02/PLAN.md). Update
  section 4 when that server exists. Section 5 names GitHub as the only
  host; update it if the site moves elsewhere or if the Cloudflare records
  below are switched to **Proxied** (Cloudflare then sees every request).
- Keep section 3 in step with the app's Apple Health reads
  (lifepace-ios: `ios/Lifepace/Model/Health.swift`, `readTypes`).

## Publish

GitHub Pages serves the site; Cloudflare only answers DNS for lifepace.eu
(registrar: webgo, nameservers: Cloudflare). The records stay **DNS only**
(grey cloud), so visitors talk to GitHub directly and GitHub issues the
HTTPS certificate.

### 1. GitHub

1. The repository must be public, or the `lifepace` organisation on a paid
   plan; GitHub Pages does not serve private repositories on the free plan.
2. Repository Settings → Pages → Source: **GitHub Actions**. The workflow
   `.github/workflows/pages.yml` deploys every tracked file except this
   README and `.github/` on every push to `main`. Run it once by hand
   (Actions → website → Run workflow) if nothing was pushed since.
3. Check <https://lifepace.github.io/> shows the landing page.

### 2. Cloudflare DNS

In the Cloudflare dashboard, lifepace.eu → DNS → Records:

1. Delete the three `A` records left over from webgo (`*`, `lifepace.eu`,
   `www`, all `185.30.32.175`). Do not keep a wildcard record: GitHub warns
   that `*` pointing at Pages lets others claim subdomains.
2. Add, all with Proxy status **DNS only**:

   | Type  | Name  | Content                 |
   |-------|-------|-------------------------|
   | A     | `@`   | `185.199.108.153`       |
   | A     | `@`   | `185.199.109.153`       |
   | A     | `@`   | `185.199.110.153`       |
   | A     | `@`   | `185.199.111.153`       |
   | AAAA  | `@`   | `2606:50c0:8000::153`   |
   | AAAA  | `@`   | `2606:50c0:8001::153`   |
   | AAAA  | `@`   | `2606:50c0:8002::153`   |
   | AAAA  | `@`   | `2606:50c0:8003::153`   |
   | CNAME | `www` | `lifepace.github.io`    |

3. Leave the mail records (`MX`, SPF `TXT`, `dkim._domainkey`, `_dmarc`)
   alone while the mailboxes live at webgo; they deliver mail for
   `hello@` and `privacy@lifepace.eu`. Replace them only together with
   the mail provider.

### 3. Custom domain

1. Repository Settings → Pages → Custom domain: `lifepace.eu`, Save. The
   `CNAME` file in this repository is not used by Actions deployments;
   this setting is what counts.
2. Wait for the DNS check to pass and the certificate to be issued (minutes
   to an hour), then tick **Enforce HTTPS**.
3. Recommended: Organisation settings → Pages → Add a verified domain
   `lifepace.eu`, and add the `TXT` record GitHub shows in Cloudflare. This
   stops other GitHub accounts from claiming the domain.

Check: `https://lifepace.eu` and `https://www.lifepace.eu` both show the
landing page with a valid certificate.
