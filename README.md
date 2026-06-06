# Pleasant Personality Academy — website

A static, single-page Astro site for **Manisha Yadav** and the Pleasant Personality Academy.
One conversion goal: get an enquiry, which emails Manisha.

> ⚠️ **Before launch**, you must do three things:
> 1. Wire the enquiry form to a real form-handling service ([§ Form service](#-form-service)).
> 2. Replace placeholder content in `src/data/*.json` and the portrait SVGs in `public/images/` ([§ Editing content](#-editing-content)).
> 3. Set the production domain in `astro.config.mjs` and `public/robots.txt` ([§ Domain](#-domain)).

---

## Quick start

```bash
npm install
npm run dev       # http://localhost:4321
npm run build     # outputs to ./dist
npm run preview   # serves ./dist locally
```

Requirements: **Node 18.20.8+, 20.3.0+, or 22.0.0+**.

---

## 📁 Project layout

```
src/
  pages/
    index.astro       # Main landing page (single-scroll)
    privacy.astro     # Privacy Policy
    404.astro         # Custom not-found page
  layouts/
    BaseLayout.astro  # <html>, <head>, SEO/OG meta, JSON-LD slot
  components/
    Header.astro      Hero.astro       About.astro
    Services.astro    Team.astro       Events.astro
    Testimonials.astro Media.astro     EnquiryForm.astro
    Footer.astro      WhatsAppButton.astro
    Icon.astro        # inline SVG icon set
  data/
    site.json         # brand, tagline, contact info, socials, stats
    services.json     # service cards
    team.json         # team members
    events.json       # upcoming events
    testimonials.json # quotes
    stories.json      # before/after long-form stories
  styles/global.css   # tokens, reset, components
public/
  favicon.svg
  og.svg              # Open Graph image (regenerate to og.png/.jpg for max compat)
  robots.txt
  images/team/*.svg   # team portrait placeholders
  images/testimonials/*.svg
```

`sitemap-index.xml` is generated at build time by the `@astrojs/sitemap` integration.

Other repo files:

| File | Purpose |
|------|---------|
| `netlify.toml` | Netlify build config, cache & security headers |
| `vercel.json` | Vercel build config, cache & security headers |
| `.github/workflows/deploy-pages.yml` | GitHub Pages CI deploy workflow |
| `.nvmrc` | Pinned Node version (22) for CI / nvm users |
| `public/site.webmanifest` | PWA manifest (icons, theme) |

---

## ✏️ Editing content

All copy lives in JSON files under `src/data/`. Edit, save, and the site rebuilds.

| File | What to edit |
|------|-------------|
| `site.json` | Brand name, tagline, contact email/WhatsApp/city, social URLs, stats |
| `services.json` | Add/remove service cards. `icon` must be one of the names in `Icon.astro` (`spark`, `people`, `mic`, `shine`, `briefcase`, `grad`) |
| `team.json` | Team members. `image` is a path under `public/images/team/` |
| `events.json` | Upcoming events. Set `status: "past"` to hide automatically. Dates are ISO `YYYY-MM-DD` |
| `testimonials.json` | Short quote cards |
| `stories.json` | Longer before/after stories (1–2 work well) |

### Replacing placeholder photos

The team and testimonial portraits in `public/images/` are stylised SVG placeholders.
Replace them with real photos:

1. Export portraits at ~600×600 (team) and 200×200 (testimonials).
2. Convert to WebP or AVIF (e.g. `cwebp -q 80 input.jpg -o output.webp`).
3. Drop into the right `public/images/...` folder.
4. Update the `image` path in the corresponding JSON file.

Real photos will boost the "credibility" half of the brief noticeably — this is the
single highest-impact swap before launch.

---

## 📨 Form service

The enquiry form posts to a placeholder endpoint. **Until you swap this in, the
form will show a success message in the browser but no email will be sent.**

Open `src/components/EnquiryForm.astro` and find:

```ts
const FORM_ENDPOINT = "https://formspree.io/f/REPLACE_WITH_YOUR_FORM_ID";
```

Pick one provider and follow its setup below.

### Option A — Formspree (recommended)

1. Sign up at [formspree.io](https://formspree.io) with Manisha's email — that mailbox receives every submission.
2. Create a new form, copy the endpoint URL (looks like `https://formspree.io/f/abcdwxyz`).
3. Replace `FORM_ENDPOINT` in `EnquiryForm.astro` with the URL.
4. The `_subject` hidden input is already wired — Formspree will use it as the email subject line.
5. Free tier = 50 submissions/month; paid tier adds reCAPTCHA + file uploads.

### Option B — Web3Forms

1. Get a free access key at [web3forms.com](https://web3forms.com).
2. Replace `FORM_ENDPOINT` with `https://api.web3forms.com/submit`.
3. Add this hidden input inside the `<form>`:
   ```html
   <input type="hidden" name="access_key" value="YOUR-ACCESS-KEY" />
   ```

### Option C — Netlify Forms (only if deploying on Netlify)

1. Leave the `action` attribute empty (or set it to `/`).
2. Add `netlify` and `name="enquiry"` attributes to the `<form>` tag.
3. Add a hidden input: `<input type="hidden" name="form-name" value="enquiry" />`.
4. Enable email notifications in Netlify dashboard → Forms → Settings → Outgoing notifications.

### Option D — Getform

Similar to Formspree — sign up, copy endpoint, replace `FORM_ENDPOINT`. Done.

> **Spam protection.** A honeypot field is built in. For high-volume protection,
> turn on the provider's built-in spam filter or add reCAPTCHA per their docs.

### Fallback: `mailto:` mode

If the placeholder endpoint is left in place, the form falls back to opening the
visitor's email client (using the `email` from `src/data/site.json`). This means
the form is **never broken** — even if you deploy before configuring a provider,
visitors can still reach the inbox. Wire up a real provider before launch for
the smoothest experience.

---

## 🌐 Domain

Two places to set the production URL:

1. `astro.config.mjs` → `site: 'https://your-domain.com'` (used for canonical URLs, sitemap, OG image absolute URLs).
2. `public/robots.txt` → update the `Sitemap:` line.

---

## 🖼️ Open Graph image

`public/og.png` (1200×630) is the default OG image and works across all social
networks including Twitter/X. The source `public/og.svg` is kept for easy
re-export — edit the SVG, then regenerate the PNG:

```bash
# macOS — uses ImageMagick
brew install imagemagick
magick -size 1200x630 public/og.svg public/og.png
```

To use a real photo of Manisha as the OG image, just replace `public/og.png`
with a 1200×630 export of your choice.

---

## 🚀 Deployment

This is a fully static site — the build output in `./dist/` deploys to any static host.

### Netlify

1. Push to GitHub.
2. New site → connect repo.
3. Build command: `npm run build` · Publish dir: `dist`.

### Vercel

1. `vercel --prod` from the project root, or connect via GitHub.
2. Framework auto-detected as Astro.

### Cloudflare Pages

1. Connect Git repo.
2. Build command: `npm run build` · Output: `dist`.

### GitHub Pages

1. Push to GitHub.
2. Enable Pages in repo settings, pointing at the GitHub Actions workflow.
3. Use the [official Astro Pages workflow](https://docs.astro.build/en/guides/deploy/github/).
4. If deploying to `user.github.io/repo-name`, set `base` in `astro.config.mjs`.

---

## 📊 Analytics (optional)

To add Google Analytics 4:

1. Get a Measurement ID (e.g. `G-XXXXXXXXXX`).
2. Add this snippet inside `<head>` in `BaseLayout.astro`:

```html
<script async src="https://www.googletagmanager.com/gtag/js?id=G-XXXXXXXXXX"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());
  gtag('config', 'G-XXXXXXXXXX', { anonymize_ip: true });
</script>
```

3. Add a cookie banner (e.g. [klaro!](https://heyklaro.com/)) if you collect any
   non-essential cookies. Update `privacy.astro` to mention analytics.

---

## ✅ Pre-launch checklist

- [ ] Replace placeholder content in `src/data/*.json` with real bios, services, events, testimonials.
- [ ] Swap all `*.svg` portraits in `public/images/team/` and `public/images/testimonials/` with real photos.
- [ ] Configure form service ([§ Form service](#-form-service)) and **test a submission lands in Manisha's inbox**.
- [ ] Update `astro.config.mjs` `site:` and `robots.txt` to the production domain.
- [ ] Convert `og.svg` → `og.png` for Twitter/X compatibility.
- [ ] Run `npm run build` and check for warnings.
- [ ] Run a Lighthouse audit on mobile (target ≥ 90 for all four scores).
- [ ] Test the enquiry form, WhatsApp button, and all anchor links from mobile.
- [ ] Verify privacy policy reflects how you actually handle data (form provider, analytics, retention).

---

## 🎨 Design tokens

Edit `src/styles/global.css` `:root { }` to change colours or fonts. Brand defaults:

- Primary: `#0F4C5C` (deep teal)
- Accent: `#E9B44C` (warm gold)
- Coral: `#E76F51`
- Cream: `#FFFAF3`
- Text: `#1F2937` (charcoal)
- Heading font: Poppins · Body font: Inter (both loaded from Google Fonts with `display=swap`)

---

## License

All content and design © Pleasant Personality Academy. Source code MIT-licensed
for your internal use.
