# Parking Sign Studio

A zero-backend, single-file web app for generating print-ready **A4-landscape parking signs**. Live preview, 8 preset templates, multilingual titles, optional QR codes, print-safe CSS.

**Live demo:** [parking-sign-generator.pages.dev/parking-demo](https://parking-sign-generator.pages.dev/parking-demo)

---

## About

I built this for a hotel that needed a quick way to generate guest-specific reserved parking signs without relying on Word templates or design software. The production version is a single-brand tool deployed at the property; this repo is the richer demo variant — same print engine, but with a template system that reconfigures the whole app (branding, colours, language, contact defaults) for different venue types.

Two files, two audiences:

- **`parking-demo.html`** — 8 venue templates (hotel, clinic, office, restaurant, residence, event, loading bay, blank), 5 languages, inline-generated SVG logos, localStorage history. The showcase version.
- **`parking.html`** — single-brand production variant with one `CONFIG` block. What actually runs at the hotel.

Both files are fully self-contained: one HTML file, inline CSS and JS, one CDN dependency (qrcodejs). No build step, no framework, no package manager.

---

## Highlights

**Print-focused design.** CSS is built around `@page { size: A4 landscape; margin: 0 }` with forced background printing, a 10 mm internal safe-area, and binary-search auto-fit typography that scales plate numbers and guest names to fit regardless of length.

**Template system (demo version).** A `TEMPLATES` object at the top of the script defines each venue preset — brand, colours, multilingual sign titles, contact role, realistic defaults, inline SVG logo. Adding a new template is a single object literal; the UI grid re-renders automatically.

**Inline SVG logos.** The demo generates template logos on the fly as SVG data URIs — no external image files, so the entire demo travels as a single HTML file you can email or drop on any static host.

**Multilingual sign titles.** English, Bahasa Malaysia, Chinese (Simplified), Tamil, and Punjabi, with Google Fonts (Noto Sans) swap-loaded for non-Latin scripts. Primary and secondary language pickers let you render bilingual signs.

**Smart QR codes.** Two optional QR slots: a main one (website preset, sign-details preset, or arbitrary string) and a contact QR that auto-detects phone numbers (→ `tel:`), emails (→ `mailto:`), or falls back to plain text.

**Date validation without being annoying.** Past dates turn red with a warning but don't block submission — backdated passes are a real use case. Expired signs show a diagonal red "EXPIRED" ribbon so the status is unmissable.

**Accessibility details.** Real `<button>` elements throughout, proper `<label for="">` pairings, `aria-pressed` / `aria-checked` on toggles, `lang` attributes on rendered titles, `role="img"` on the preview canvas, visible focus rings.

**Persistence.** Last-used template, collapsed-section state, and the last 10 printed signs are saved to `localStorage`. No server, no tracking, no analytics.

---

## Tech notes

| | |
|---|---|
| Stack | Vanilla HTML, CSS, JS. No framework. |
| Dependencies | qrcodejs (CDN, ~15 KB). Google Fonts for CJK/Tamil/Gurmukhi scripts. |
| Size | ~55 KB per file, uncompressed. |
| Print target | A4 landscape (297 × 210 mm), colour and B&W modes. |
| Browser support | Chrome, Edge, Firefox, Safari. Not IE. |
| Storage | `localStorage` for prefs and history. Fully functional without it. |

---

## Running locally

```bash
# Just open the file
open parking-demo.html          # macOS
start parking-demo.html         # Windows
xdg-open parking-demo.html      # Linux

# Or serve it
python3 -m http.server 8000
# then browse to http://localhost:8000/parking-demo.html
```

No install step. No build step.

---

## Adding a template (demo version)

Open `parking-demo.html`, find the `TEMPLATES` object near the top of the `<script>` block, and add an entry:

```js
school: {
  name: "School",
  brand: {
    name: "Riverside Academy",
    enforcementText: "Staff and registered visitors only",
    websiteUrl: "https://example.edu"
  },
  contact: {
    label: "School Office",
    value: "+60 3-1234 5678"
  },
  signTitles: {
    en: "STAFF PARKING",
    bm: "PARKIR KAKITANGAN",
    zh: "教职员工停车位",
    ta: "ஊழியர் பார்க்கிங்",
    pa: "ਸਟਾਫ਼ ਪਾਰਕਿੰਗ"
  },
  colors: { accent: "#0369a1", dark: "#0c4a6e" },
  logo:   { text: "RA", color: "#0369a1" },
  defaults: {
    plate: "WQR 4501",
    guestName: "TEACHING STAFF",
    roomLabel: "Block"
  }
}
```

Reload. The new card appears in the template grid.

---

## Design decisions worth noting

**Why inline SVG logos instead of image files?** The demo is meant to be evaluated in isolation — someone can email the HTML file to a colleague and it just works, no broken image links. Real brands would swap in a proper logo image via `logoImage: "..."` on any template.

**Why `DD/MM/YYYY` dates?** The target audience is Malaysian and wider Asia-Pacific; MM/DD gets misread, and front-desk staff shouldn't have to think about formatting. ISO-style (`YYYY-MM-DD`) is accurate but cold on a printed sign.

**Why allow past dates?** Retroactive parking passes are common — a guest stays late, a delivery runs over, a visitor disputes a tow. Warning is better than blocking.

**Why a 10 mm internal frame instead of edge-to-edge?** Many office printers can't print edge-to-edge. The frame guarantees nothing important clips regardless of printer.

**Why localStorage instead of a cookie or backend?** The tool should run entirely offline. Printing parking signs shouldn't depend on a server being up.

---

## File structure

```
.
├── parking-demo.html       Multi-template demo version (recommended)
├── parking.html            Single-brand production variant
├── assets/
│   └── logo.png            Hotel logo used by parking.html
└── README.md               This file
```

---

## Licence

MIT. Use it, fork it, rebrand it, ship it.
