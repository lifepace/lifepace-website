# lifepace.eu

The public website: a landing page, the privacy policy and the imprint.
Plain HTML and one stylesheet, no build step, no cookies, no external
requests.

```text
index.html     landing page
privacy.html   privacy policy (app, planned Garmin connection, website)
imprint.html   Impressum (§ 5 DDG)
style.css      the app's palette: navy #0E1829, mint #5EE0B8
icon.svg       the app icon, copied from lifepace-ios ios/design/AppIcon.svg
CNAME          lifepace.eu, for GitHub Pages
```

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
   `.github/workflows/pages.yml` deploys the site on every push to `main`.
2. At the domain registrar for lifepace.eu, add these DNS records:
   - four `A` records for `@`: 185.199.108.153, 185.199.109.153,
     185.199.110.153, 185.199.111.153;
   - a `CNAME` record for `www`: `lifepace.github.io`.
3. Settings → Pages → Custom domain: `lifepace.eu`, then tick **Enforce
   HTTPS** once the certificate is issued.
