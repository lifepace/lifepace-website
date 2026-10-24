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
CNAME                    lifepace.eu
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
  section 4 when that server exists, and section 5 if the site is not
  hosted on GitHub Pages.
- Keep section 3 in step with the app's Apple Health reads
  (lifepace-ios: `ios/Lifepace/Model/Health.swift`, `readTypes`).

## Publish on GitHub Pages

1. Repository Settings → Pages → Source: **GitHub Actions**. The workflow
   `.github/workflows/pages.yml` deploys every tracked file except this
   README and `.github/` on every push to `main`. GitHub Pages needs the
   repository public, or a paid plan.
2. At the domain registrar for lifepace.eu, add these DNS records:
   - four `A` records for `@`: 185.199.108.153, 185.199.109.153,
     185.199.110.153, 185.199.111.153;
   - a `CNAME` record for `www`: `lifepace.github.io`.
3. Settings → Pages → Custom domain: `lifepace.eu`, then tick **Enforce
   HTTPS** once the certificate is issued.
