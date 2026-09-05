# Online Clothing Store Website — Template

A static, Bootstrap 5 storefront template for clothing brands. All client-specific
content has been replaced with `[TOKEN]` placeholders — search and replace them to
brand a new site.

## Quick start

```bash
# swap the brand name across every page
grep -rl '\[BRAND_NAME\]' --include='*.html' . | xargs sed -i 's/\[BRAND_NAME\]/Acme Apparel/g'
```

Then work down the token table below. To find anything still unfilled:

```bash
grep -rnoE '\[[A-Z_]+\]' --include='*.html' . | sort -u
```

## Placeholder tokens

| Token | Where | What to put |
| --- | --- | --- |
| `[BRAND_NAME]` | every page — `<title>`, nav logo alt, footer copyright, about/terms/privacy copy | The store's display name |
| `[BRAND_SHORT_DESCRIPTION]` | product pages, Instagram follow block | One sentence describing the brand |
| `[BRAND_TAGLINE]` | product pages, Instagram follow block | Short tagline / slogan |
| `[SITE_DESCRIPTION]` | `<meta name="description">` on every page | SEO description |
| `[SITE_KEYWORDS]` | `<meta name="keywords">` on every page | Comma-separated SEO keywords |
| `[CONTACT_EMAIL]` | `privacy.html` | Support / enquiries address |
| `[INSTAGRAM_HANDLE]` | header + footer social row, product pages | Instagram username only (no `@`) |
| `[FACEBOOK_HANDLE]` | header + footer social row | Facebook page slug or `profile.php?id=…` |
| `[X_HANDLE]` | header + footer social row | X/Twitter username only (no `@`) |
| `[GA_MEASUREMENT_ID]` | `index.html` gtag snippet | Google Analytics 4 ID, e.g. `G-XXXXXXXXXX` |
| `[FORM_ENDPOINT_URL]` | `index.html`, `contact.html`, `products.html` | POST endpoint for the newsletter/contact forms (this build used a Google Apps Script `…/exec` URL) |
| `[PRODUCT_NAME]` | `inStock-pages/*.html`, `pre-orders-pages/*.html`, listing cards | Per-page product name — each page holds one product |
| `[PRODUCT_DESCRIPTION]` | `pre-orders-pages/*.html` | Per-page product blurb |
| `[COLLECTION_NAME]` | `about.html` collections grid | Collection / capsule name |
| `[STRIPE_PAYMENT_LINK_S]` … `_M`, `_L`, `_XL`, `_XXL` | `inStock-pages/*.html` size selector script | One Stripe Payment Link per size (reuse the same URL for all five if sizing is handled inside Stripe) |

The GitHub icon in the social row still points at `https://github.com/d-velopr`
(the developer's account, not the client's) — remove or repoint it per build.

## Structure

```
index.html              Shop / home
about.html              Brand story + collections
discover.html           Category + product browse
products.html           Mega-menu product index
pre-orders.html         Pre-order listing
contact.html            Contact form
faq.html  terms.html  privacy.html  404.html
inStock-pages/          One page per in-stock product (stock1–7)
  full-template.html    Blank product page to copy for new products
pre-orders-pages/       One page per pre-order product (pre-order1–4)
css/style.css           Custom styles (Bootstrap overrides)
js/script.js            Swiper sliders, product detail, qty controls
img/                    Assets — see below
vender/                 Bootstrap, jQuery, Remix Icon, Swiper
```

## Assets

Every page renders out of the box — no broken images. Two placeholder assets stand in
for everything brand- or product-specific.

### The logo — `img/logo.svg`

One file, used everywhere a brand mark appears: the navbar on all 22 pages, the
`<link rel="icon">` fallback, the brand mark on product detail pages, and the avatar in
the "Follow us on Instagram" block. Drop the client's logo in at this path and it
propagates. It's a square-ish tile; the navbar renders it at 66px wide
(`.osahan-nav .logo`) and product pages at 150px tall (`.product-page-logo`,
`object-fit: contain`, so any aspect ratio is safe).

### Favicons — `img/fav/`

`img/fav/favicon-source.svg` is the source mark. The 24 PNGs and `favicon.ico` beside it
are generated from it — edit the source, then regenerate:

```bash
python3 - <<'PY'
import cairosvg, glob, os, re
from PIL import Image
src = 'img/fav/favicon-source.svg'
for p in sorted(glob.glob('img/fav/*.png')):
    m = re.search(r'(\d+)x(\d+)', os.path.basename(p))
    n = int(m.group(1)) if m else 192
    cairosvg.svg2png(url=src, write_to=p, output_width=n, output_height=n)
cairosvg.svg2png(url=src, write_to='/tmp/ico.png', output_width=256, output_height=256)
Image.open('/tmp/ico.png').save('img/fav/favicon.ico',
    sizes=[(16,16),(32,32),(48,48),(64,64),(128,128),(256,256)])
PY
```

Also set `"name"` in `img/fav/manifest.json` (currently `[BRAND_NAME]`) and the
`<TileColor>` in `img/fav/browserconfig.xml`.

### Imagery — `img/misc/na.png`

The "Not Available" blank-tee placeholder. Every image slot with no real asset points
here, including four CSS backgrounds (`.hero-slider-one`, `.bg-collections`,
`.bg-single-collection`, `.brand-header`). Find them all with:

```bash
grep -rn 'img/misc/na\.png' --include='*.html' --include='*.css' .
```

Real imagery already in the repo, safe to keep or replace:

- `img/placeholders/` — 4 blank garment mockups, used on the home page
- `img/pre-orders/` — 4 blank garment shots, used on `products.html`
- `img/*-icon.svg`, `img/404.svg`, `img/done.svg` — generic UI illustrations

## Checkout

In-stock product pages wire the size radios to Stripe Payment Links in an inline
`<script>` near the bottom of each page:

```js
const links = {
  btnradio1ss: "[STRIPE_PAYMENT_LINK_S]",  // S
  btnradio2ss: "[STRIPE_PAYMENT_LINK_M]",  // M
  ...
};
```

There is no cart or server — each size links straight out to Stripe.
