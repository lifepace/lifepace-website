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

- Check that `info@lifepace.eu` (imprint and privacy policy) receives
  mail. The domain's MX points to webgo (`s175.goserver.host`); the
  mailbox or forward must exist there. The MX accepts any recipient at
  `RCPT TO` (an invented address gets `250` too), so only a test mail
  that arrives in the inbox proves it.
- Imprint and privacy policy name Martin Jöhren and Alexander Schardt as
  private persons. Update both pages once a company is registered (name,
  legal form, register court and number, VAT ID).
- Have the privacy policy reviewed. It describes LifePace as it will work
  at launch: an account, plans made on the server, and the athlete record
  of Tree M (decisions M1-M4 in the engine's
  docs/review-2026-10-02/PLAN.md). Section 9 still has to name the hosting
  provider, the sign-in provider and the weather service. Section 10
  names GitHub as the only host of this site; update it if the site moves
  elsewhere or if the Cloudflare records below are switched to
  **Proxied** (Cloudflare then sees every request). The review also
  checks:
  - how the two named operators are presented: without a company,
    running LifePace together may make them a GbR or joint controllers
    (Art. 26 GDPR), which can need a different name in section 1 and the
    imprint, or a written arrangement between them.
- Keep section 4's Apple Health table in step with the app's reads
  (lifepace-ios: `ios/Lifepace/Model/Health.swift`, `readTypes`).

## Promises the product must keep

The home page and the privacy policy describe LifePace at launch. Each
promise below is either built in the engine or planned there; the app and
the engine have to deliver it before launch, or the page changes first.

| Promise (page) | Engine state |
| --- | --- |
| Plans from Apple Health runs or runs entered by hand (home) | runs and `usual_easy_run` built; manual entry is Tree M M-e |
| First walk-run to marathon (home) | walk-run rungs and goals `general` to `marathon` built |
| Morning check-in and pain report change today's session (home) | built (docs/10 §5) |
| Pregnancy, postpartum, menopause, pelvic floor, bone (home) | built (docs/10 §9) |
| Rules traced to a source or marked as our choice; plan says why it changed (home) | evidence labels and plan notes built; no LLM in the planning path |
| Account, EU sign-in provider, only the subject id stored (policy 3) | Tree M M1, M-c; provider not named (`lifepace-qav.14`) |
| Consent per health topic, fail closed, withdrawal deletes queued data too (policy 5) | Tree M M3, M-d. M3's topics do not yet cover every health input (screening, injuries, body measurements): map each before launch |
| EU hosting, per-user key for health data, audited access (policy 6) | Tree M M4, M-b, M-c |
| 24-month deletion with a 30-day email, raw device data 90 days, plan versions kept (policy 7) | Tree M M4, M-b, M-g |
| Export, withdraw consent and delete the account in the app (policy 11) | Tree M M-b (export, deletion), M-d |
| Garmin: activities; sleep, resting heart rate, HRV status with consent (policy 4) | Tree M M2, M-f; needs Garmin partner approval |
| Race weather: only the location rounded to about 11 km leaves (policy 9) | built (docs/10 §9, "Handling the data"); off until launch |
| Usage statistics only after the user switches on "Help improve LifePace", off by default (policy 2, 8) | `/v1/stats` built; the app sends it today without asking (owner decision 2026-10-25: opt-in). The app change is in lifepace-ios, not the engine |

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
   `info@lifepace.eu`. Replace them only together with the mail provider.

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
