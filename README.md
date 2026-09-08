# joyleaf.com — static clone

**Preview:** https://sgencms.github.io/client-joyleaf

Static clone of `https://joyleaf.com/`. No build step — open `index.html` or serve the directory.

Single-page clone: only the homepage was cloned.

## Fidelity

Compared against the live source with `pixelmatch`, at the six canonical breakpoints, served from
the `/client-joyleaf/` subpath this site is published under.

Each capture clips to exactly the viewport column at `deviceScaleFactor: 1` with reduced motion.
A bare full-page screenshot is **not** usable on this site: it has horizontal overflow from four
carousels whose track width depends on when the slider JS settles, so `document.scrollWidth` — and
therefore the image width — is nondeterministic. Three captures of the same URL came back 2645,
1559 and 1300 px wide with identical heights. Clipping to the viewport column pins the width by
construction, so both sides are dimensionally identical and nothing is silently cropped.

The live source is also captured **twice** per local run, so its own run-to-run variance is
measured rather than assumed. Three runs: two against the local bundle, and a third against this
**published** site as actually served by GitHub Pages.

| Viewport | Clone vs source (local 1 / local 2 / **published**) | Source vs *itself* (local 1 / local 2) |
|---|---|---|
| 1440x7585 | 100.000% / 100.000% / 99.774% | 100.000% / 99.705% |
| 1280x7849 | 99.754% / 99.752% / 99.712% | 99.993% / 99.660% |
| 1024x8606 | **100.000% / 100.000% / 100.000%** | 100.000% / 100.000% |
| 768x9259 | **100.000% / 100.000% / 100.000%** | 100.000% / 100.000% |
| 430x6129 | **100.000% / 100.000% / 100.000%** | 100.000% / 100.000% |
| 390x6277 | **100.000% / 100.000% / 100.000%** | 100.000% / 100.000% |

Four of the six breakpoints are **exact — 0 mismatched pixels of 2.4M–8.8M, in all three runs.**

**The two desktop widths differ only in one component: the testimonial carousel.** Locating the
differing pixels by row band:

- 1280, published run: 28,979 px (0.288%), **100% of them** inside y=1100–1399.
- 1440, published run: 24,719 px (0.226%), **24,521 of them (99.2%)** inside y=1100–1399; the
  remaining 198 px are scattered in the hero at y=200–499.

That band holds a `testimonial-items … layout-carousel … swiper` with 4 slides, measured at
y=1142–1434. The carousel is simply resting on a different slide. This is dynamic content, not
clone infidelity, and it is **inside the source's own variance**: on consecutive loads the live
source differed *from itself* by 32,259 px at 1440 (99.705%) and 34,118 px at 1280 (99.660%) —
in both cases more than the clone differs from the source. At 1440 the clone matched the source
*exactly* in two of the three runs.

All 12 acceptance gates pass, with 0 hard issues.

## Hosting

`.nojekyll` is **required and must not be deleted.** GitHub Pages runs Jekyll by default, which
ignores any path beginning with `_`. That would silently drop the whole `_xorigin/` tree — jQuery,
the Bootstrap bundle and all six Work Sans webfont faces — producing a page that throws
`ReferenceError` on its accordions and renders in fallback fonts, with no build error anywhere.

The bundle is served from the repository root, so `index.html`, `chrome.css` and the asset tree sit
at the top level.

## What differs from source

This is a preview mirror, not a working storefront. Deliberate changes:

- **Self-contained at load.** With every non-localhost request blocked and recorded, the page
  issued **zero off-origin HTTP requests** and had **zero broken images**. Two
  `<link rel="preconnect">` hints to `fonts.googleapis.com` / `fonts.gstatic.com` do remain in the
  head; they open a DNS/TCP/TLS connection but fetch nothing, as all six webfont faces are served
  locally from `_xorigin/`.
- **Subresource Integrity stripped from the two localized CDN scripts** (jQuery 3.7.0, Bootstrap
  5.3.8). Their `src` now points at a local relative path, and SRI is only honoured on a
  CORS-enabled fetch — left in place, both scripts failed with `net::ERR_FAILED` and `window.jQuery`
  was `undefined`. With them removed the bundle reports jQuery `3.7.0`, matching the live site.
- **Analytics and tracking removed** from the executing page: 8 tracking scripts stripped at emit
  plus 1 `<noscript>` tracker.
- **Runtime origins stripped** from 8 inline config objects, so requests built at runtime stay on
  whichever origin serves this bundle.
- **Forms are inert.** The one form on the page is neutralised.
- **`canonical` and `og:url` repointed at the live source.** The emitted clone self-canonicalised
  to `index.html`, which would tell search engines this mirror is the authoritative copy of the
  client's homepage. It now points at `https://joyleaf.com/`.
- **Six root-relative navigation links repointed at the live source** (`/delivery` ×2,
  `/privacy-policy`, `/terms-of-service`, and one image link to `/`). Served from a project subpath
  these resolved to `https://sgencms.github.io/...` and 404ed.

Preserved verbatim: all markup, styling, imagery, and the site's own presentation JS.

## Known limitations

- **Single page.** Links to other routes point at the live site.
- **Cart is not live.** The product widget POSTs to `dispenza/ajax/cart_html`; a static host cannot
  serve it, so GitHub Pages answers `405` and the cart stays empty. This is the only console error
  on the published page, and the request does not reach `joyleaf.com`.
- **Fifteen unused popups** (`sgp_1`–`sgp_15`) carry the AJAX flag. Nothing on this page opens them,
  so they cannot fire or fail; their bodies were not captured rather than invented.
- **~2.0 MB of third-party tracker payloads still sit in `_xorigin/`** (Google Tag Manager, Microsoft
  Clarity, Adelphic), including the client's GTM container configuration. They are **referenced 0
  times** by `index.html` and never execute — they are capture residue, not live tracking. They were
  kept rather than deleted to preserve the byte-fidelity of the capture; delete `_xorigin/`'s
  `www.googletagmanager.com`, `scripts.clarity.ms` and `js.ipredictive.com` subtrees if you would
  rather they were not published.
- **Point-in-time.** Menu contents, pricing, promotions and dated terms reflect the source at clone
  time and will drift. Some promotional terms carry fixed dates.
- **Robots meta is inherited from source** (`index, follow`). The `canonical` now points at the live
  site, which is what consolidates search signals back to the client, but if you do not want this
  mirror crawled at all, add a `noindex` meta or a `robots.txt`.

## Ownership

Client content, cloned for preview. Rights remain with the client. Confirm permission before
circulating this URL.
