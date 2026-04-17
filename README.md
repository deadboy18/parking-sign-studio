# Reserved Parking Sign Generator

A zero-backend, single-file web app for hotels, offices, and venues to generate printable **A4-landscape reserved-parking signs**. Fill in the form, preview updates live, hit print.

![A4 landscape · Print-ready](https://img.shields.io/badge/format-A4%20Landscape-0f172a)
![No backend](https://img.shields.io/badge/stack-static%20HTML-facc15)

---

## Features

- **Live preview** at true A4 landscape size (297 × 210 mm)
- **Colour & B&W modes** — toggle between accent-coloured and pure-ink output (saves toner, looks sharp on photocopies)
- **Optional QR code** with presets for your website or auto-encoding the sign details (plate / guest / validity) for scan-verification
- **Room number badge** — optional bordered tag next to guest name
- **Notes line** — small italic line for extra instructions ("Loading dock", "Level B2", etc.)
- **Quick-date chips** — Today, +1 Night, +2 Nights, +1 Week
- **DD/MM/YYYY** date formatting
- **Auto-fit typography** — long plates and long guest names shrink smoothly via binary-search sizing
- **Print-safe CSS** — `@page A4 landscape` with zero margins and forced background printing
- **Fully configurable** — one `CONFIG` block at the top of the script lets you rebrand for any organisation in a minute
- **Keyboard shortcut** — `Ctrl/Cmd + Enter` to print

---

## Quick start (Hotel Neo+ Penang, out of the box)

1. Open `parking.html` in a browser. Done. It works standalone from disk, no server needed.
2. Or deploy the folder as-is (see [Deployment](#deployment) below).

## Customising for a different organisation

You only ever need to edit **one block**: the `CONFIG` object near the top of the `<script>` section in `parking.html`.

```js
const CONFIG = {
  brand: {
    name:            "Your Hotel Name",
    logoUrl:         "assets/logo.png",            // PNG / JPG / SVG, transparent PNG works best
    faviconUrl:      "https://example.com/favicon.ico",
    websiteUrl:      "https://example.com",        // used by the "Website" QR preset
    enforcementText: "Unauthorised vehicles will be towed at owner's expense",
    signTitle:       "RESERVED PARKING"            // big headline — could be "VISITOR PARKING", "STAFF PARKING", "LOADING BAY"
  },
  colors: {
    accent: "#facc15",   // primary highlight — ignored in B&W mode
    dark:   "#0f172a"    // near-black used for text and frames
  },
  defaults: {
    plate:     "ABC 1234",
    guestName: "VIP GUEST"
  }
};
```

### Replacing the logo

Drop your logo into the `assets/` folder (or anywhere you like) and point `CONFIG.brand.logoUrl` at it.

Supported:
- Relative path — `"assets/logo.png"` (recommended for repo deployment)
- Absolute URL — `"https://example.com/logo.svg"`
- Inlined data-URI — `"data:image/png;base64,…"` (useful if you want a truly single-file distributable)

**Logo tips**
- Transparent PNG or SVG gives the cleanest print
- Wide/landscape logos work best since the header slot is ~22 mm tall
- The same image is used as a faint watermark behind the sign — a strong silhouette helps here

### Changing the accent colour

Edit `CONFIG.colors.accent`. Any hex value works. Examples:

| Brand feel     | Accent      |
| -------------- | ----------- |
| Hotel / yellow | `#facc15`   |
| Corporate blue | `#2563eb`   |
| Luxury gold    | `#c9a227`   |
| Red alert      | `#dc2626`   |
| Forest green   | `#16a34a`   |

B&W mode automatically overrides this with pure black, so whatever you pick is safe for colour printing without breaking monochrome.

---

## Deployment

### Cloudflare Pages (recommended, free)

1. Push this folder to a GitHub or GitLab repository
2. Log in to Cloudflare → Workers & Pages → Create → Pages → Connect to Git
3. Pick your repo
4. **Framework preset:** None
5. **Build command:** *(leave blank)*
6. **Build output directory:** `/`
7. Save and Deploy

Your sign generator is live at `https://<project>.pages.dev`. Every push to `main` redeploys automatically.

### GitHub Pages

1. Push to GitHub
2. Repo → Settings → Pages
3. Source: **Deploy from a branch** → `main` → `/ (root)`
4. Save. URL appears after a minute at `https://<user>.github.io/<repo>/parking.html`

### Any static host

The entire app is just HTML + CSS + JS + one image. Drop the folder on:
- Netlify — drag-and-drop the folder onto netlify.com/drop
- Vercel — `vercel` in the folder
- S3 + CloudFront — upload as static files
- A plain Apache/nginx box — copy to the web root

### Run locally

```bash
# Option 1: just open the file
open parking.html          # macOS
start parking.html         # Windows
xdg-open parking.html      # Linux

# Option 2: any static server
python3 -m http.server 8000
# then browse to http://localhost:8000/parking.html
```

---

## File structure

```
.
├── parking.html      The entire app — HTML, CSS, JS inline
├── assets/
│   └── logo.png      Your organisation logo (header + watermark)
└── README.md         This file
```

That's the whole project. No build step, no dependencies to install, no framework.

The one external dependency is the **qrcodejs** library, loaded from the jsDelivr CDN inside `parking.html`. If you need full offline operation, download [qrcode.min.js](https://cdn.jsdelivr.net/npm/qrcodejs@1.0.0/qrcode.min.js), save it next to `parking.html`, and change the `<script src="…">` line to a local path.

---

## Printing tips

- **Paper:** A4, landscape. The tool is strictly sized for A4 — do not use Letter or other sizes.
- **Margins:** the CSS sets `@page { margin: 0 }`. In the browser print dialog, set **Margins → Default** (or None). If your printer can't print edge-to-edge, the 10mm internal frame means nothing important gets clipped.
- **Background graphics:** must be **ON**. This is the single most common reason signs print wrong. In Chrome/Edge the checkbox is under **More settings → Background graphics**. Turn it on every time.
- **Colour vs B&W:** pick the theme in the sidebar *before* printing. Colour mode relies on printable accent colour; B&W mode assumes a laser or monochrome inkjet.
- **Chrome/Edge** give the cleanest output. Firefox and Safari work but occasionally shift margins by 1–2 mm.
- **PDF export:** in the print dialog, pick "Save as PDF" as the destination. You get a perfect A4-landscape PDF you can email or archive.

---

## QR code notes

The QR code is **optional** — leave the field blank and it disappears from the sign.

When filled in, it renders as a 20 × 20 mm square in the footer. Three quick presets are provided:

| Preset         | Content encoded                                                                 |
| -------------- | ------------------------------------------------------------------------------- |
| **Website**    | Your `CONFIG.brand.websiteUrl`                                                  |
| **Sign details** | `Plate: … \| Guest: … \| Room: … \| From: … \| To: …` (useful for security scanning and cross-checking against a register) |
| **Clear**      | Empties the field                                                               |

You can also type **anything** into the box — a URL, a WhatsApp link (`https://wa.me/60123456789`), a tel: link, a vCard, whatever makes sense for your operation.

---

## Browser support

| Browser         | Supported   | Notes                                    |
| --------------- | ----------- | ---------------------------------------- |
| Chrome / Edge   | ✅ Full     | Best print output                         |
| Firefox         | ✅ Full     | Minor margin drift possible               |
| Safari          | ✅ Full     | Test-print once before rolling out         |
| IE 11           | ❌          | Uses ES6 + CSS custom properties          |

---

## Licence

Use it, fork it, rebrand it, ship it. No attribution required.
