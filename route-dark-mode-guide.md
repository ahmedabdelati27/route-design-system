# Route — Dark Mode Guide

This file explains how to build any Route screen so it works in **light and dark** mode. The colors live in [`route-dark-mode-tokens.css`](./route-dark-mode-tokens.css). That file mirrors the Figma variables in **Route Landing Page (Copy)**, collection **Mapped** (Light Mode / Dark Mode).

**Rule zero:** never write a hex color directly in a component. Every color comes from a token (`var(--color-…)`). Any hardcoded color will stay the same in dark and break the screen.

---

## 1. Setup

**1. Load the tokens:**
```html
<link rel="stylesheet" href="route-dark-mode-tokens.css">
```

**2. Apply the saved preference before the page paints**, so there's no white flash. Put this in `<head>` before the CSS:
```html
<script>
  try { const t = localStorage.getItem('theme'); if (t) document.documentElement.dataset.theme = t; } catch (e) {}
</script>
```

**3. Theme toggle button** (the moon in the header):
```js
function toggleTheme() {
  const root = document.documentElement;
  const isDark = root.dataset.theme === 'dark' ||
    (!root.dataset.theme && matchMedia('(prefers-color-scheme: dark)').matches);
  root.dataset.theme = isDark ? 'light' : 'dark';
  try { localStorage.setItem('theme', root.dataset.theme); } catch (e) {}
}
```

**How the mode is decided:**
- If the user picked one, `data-theme` wins.
- If they didn't pick, it follows the device's setting.
- `data-theme="light"` forces light even when the device is dark.

---

## 2. Backgrounds and elevation (the most important part)

Dark mode doesn't use shadows to separate layers. It uses **getting lighter step by step**: the higher the level, the lighter the color.

| Level | Light | Dark | Token |
|---|---|---|---|
| Page | `#ffffff` | `#000000` | `--color-surface-page` |
| Dashboard / panel canvas behind cards | `#f2f2f2` | `#000000` | `--color-surface-background` |
| Card / sidebar / form / top bar | `#ffffff` | `#191919` | `--color-surface-card` |
| Element inside a card (chip, subtle fill) | `#f2f2f2` | `#191919` | `--color-surface-shade-1` |
| Raised element on a card (thumbnail, input, hover) | `#e5e5e5` | `#333333` | `--color-surface-shade-2` |
| Highest level (tooltip, selected) | `#cccccc` | `#4d4d4d` | `--color-surface-shade-3` |

**The most common mistakes:**
- **A card on the page:** use `--color-surface-card`, **not** `--color-surface-page`. They look the same in light, but in dark the card disappears.
- **An element inside a card that has to stand out:** use `shade-2`. `shade-1` comes out the same color as the card in dark.
- **A section with a grey background in light** (the dashboards and the Contact panel): the background is `--color-surface-background` and the content is `--color-surface-card`.
- **Shadows** (`--shadow-1`) are nearly invisible on black. Don't rely on them to separate a card in dark. Separation comes from the background color, plus `--color-border-default` if needed.

---

## 3. Which token goes where

### Text
| Use | Token | Light | Dark |
|---|---|---|---|
| Headings, card titles | `--color-text-headings` | `#191919` | `#f2f2f2` |
| Paragraphs | `--color-text-body-1` | `#666666` | `#cccccc` |
| Captions, dates, meta | `--color-text-body-3` | `#999999` | `#cccccc` |
| Placeholder | `--color-text-body-4` | `#b3b3b3` | `#999999` |
| Links, highlighted word in a heading | `--color-text-action` | `#00298e` | `#e9f4ff` |
| Text on a blue button | `--color-text-on-action` | `#ffffff` | `#ffffff` |
| Label above a section title ("Why Route?") | `--color-text-eyebrow` | `#00b2e4` | `#00b2e4` |

### Buttons
| Type | Background | Text / icon | Border |
|---|---|---|---|
| Primary | `--color-surface-action` (hover: `-action-hover`) | `--color-text-on-action` | — |
| Outline (Login, Course Details) | transparent | `--color-text-action` | `--color-border-action` |
| White circle with an arrow inside the blue button | `--color-fixed-white` | `--color-surface-action` ⚠️ | — |

⚠️ The arrow inside the white circle takes **`--color-surface-action`**, not `--color-icon-action`. `icon-action` turns white in dark, and the arrow would disappear inside the white circle.

### Icons
- Normal icons: `--color-icon-heading` / `--color-icon-default`.
- Brand icons, decorative patterns, logo letters: `--color-icon-action`.
- Footer social circles: background `--color-surface-action` and a white icon.

### Borders
- Card borders and dividers: `--color-border-default`.
- Focus ring: `--color-border-focus`.

---

## 4. Special cases from the design

1. **Opacity is separate from color.** A layer like "white at 20%" over images becomes:
   ```css
   background: color-mix(in srgb, var(--color-surface-page) 20%, transparent);
   ```
   Never replace it with a solid color.
2. **Fades** (bottom of the hero, edges of the testimonials) use the page color, not white:
   ```css
   background: linear-gradient(to bottom, transparent, var(--color-surface-page));
   ```
3. **The big ROUTE letters** (hero and footer):
   ```css
   background: linear-gradient(90deg, var(--color-surface-page), var(--color-surface-shade-1), var(--color-surface-page));
   ```
4. **Decorative patterns** (the 0/1s in the course card header and the "14+" card): `--color-icon-action`, with the layer's original opacity (6–7%).
5. **Course card header:** `--color-surface-action-light`. It's light blue in light and `#1c2431` in dark.
6. **The "Noise & Texture" texture** stays at **3% opacity**. Higher than that looks like TV static in dark.
7. **Graduate video carousel:** the side cards have a 20% overlay in the page color (see point 1).

### Things that stay the same in both modes (never bind them to theme tokens)
- Partner logos and the Engineers Syndicate logo, which stay on a white plate (`--color-fixed-white`).
- Language icons (JS, React, Angular …) and the 3D images.
- The ChatGPT / Claude / Gemini pills, which always have a white background.
- The blue "AI in every diploma" card and the brand cards (`--color-fixed-brand`).
- Photos, and the white frames that are baked into the images.

---

## 5. Full example: course card

```html
<article class="course-card">
  <div class="course-card__header">
    <span class="course-card__eyebrow">Diploma</span>
    <h3>Front End Development</h3>
  </div>
  <div class="course-card__body">
    <h4>Frontend Development</h4>
    <p class="course-card__meta">Beginner Level · 5 months</p>
    <span class="chip">HTML &amp; CSS</span>
    <div class="course-card__footer">
      <strong>7,000 EGP</strong>
      <a class="btn-outline" href="#">Course Details</a>
    </div>
  </div>
</article>
```

```css
.course-card {
  background: var(--color-surface-card);           /* white → #191919 */
  border: 1px solid var(--color-border-default);
  border-radius: 16px;
  overflow: hidden;
}
.course-card__header {
  position: relative;
  background: var(--color-surface-action-light);   /* light blue → #1c2431 */
}
.course-card__header::before {                     /* decorative pattern */
  content: ""; position: absolute; inset: 0;
  background: url(pattern.svg) center / cover;
  opacity: .07;
  /* if the pattern is an inline SVG: fill: var(--color-icon-action) */
}
.course-card__eyebrow { color: var(--color-text-eyebrow); }
.course-card h3, .course-card h4 { color: var(--color-text-headings); }
.course-card__meta { color: var(--color-text-body-3); }
.chip {
  background: var(--color-surface-shade-1);
  border: 1px solid var(--color-border-default);
  color: var(--color-text-body-1);
}
.btn-outline {
  color: var(--color-text-action);
  border: 1px solid var(--color-border-action);
}
.course-card__footer {
  border-top: 1px solid var(--color-border-default);
  color: var(--color-text-headings);
}
```

---

## 6. How to check a screen works in dark

1. In DevTools, run `document.documentElement.dataset.theme = 'dark'` and go over the whole page.
2. Search the CSS for hex colors: `grep -nE '#[0-9a-fA-F]{3,8}\b' *.css *.html`. Any color outside the tokens file is a likely problem, unless it's on the "stays the same" list.
3. Check these things specifically:
   - Is any card the same color as the page?
   - Is there any dark text on a dark background?
   - Is there a white fade or overlay?
   - Does the arrow inside the button chip show?
4. Switch the device itself to dark and make sure the page follows it when no `data-theme` is set.

---

## 7. Migrating the current site's variables

The current pages use variables that have no dark mode. Replace them like this:

| Old variable | Value | New token |
|---|---|---|
| `--bg` | `#FFFFFF` | `--color-surface-page` (or `--color-surface-card` for a card) |
| `--alt` | `#F9FAFD` / `#E9F4FF` | `--color-surface-shade-1` / `--color-surface-action-light` |
| `--soft`, `--cyan-soft` | `#EEF3FF`, `#E6F4FB` | `--color-surface-action-light` |
| `--grid` | `#EEF1F6` | `--color-surface-shade-1` |
| `--dim` | `#DCE2EE` | `--color-surface-shade-2` |
| `--primary` | `#00298E` | `--color-surface-action` (background) / `--color-text-action` (text) / `--color-icon-action` (icon) |
| `--primary-deep` | `#001F6B` | `--color-surface-action-hover` |
| `--ink` | `#0E1320` | `--color-text-headings` |
| `--body`, `--muted` | `#4A5366`, `#5A6378` | `--color-text-body-1` |
| `--line`, `--line-2` | `#E4E7EE`, `#D5DAE4` | `--color-border-default` |
| `--alt-line` | `#D3E6FA` | `--color-border-action-light` |
| `--cyan`, `--pill` | `#1AA0DB`, `#0B78B8` | `--color-text-eyebrow` |
| `--sky`, `--cyan-ink` | decorative | leave as is (fixed in both modes) |

Hardcoded colors that repeat in the design:
- `#161b26`, `#0b1020`, `#0e1320`, `#101323` → `--color-text-headings`
- `#5a6378`, `#5b6272`, `#626a80`, `#2a3142` → `--color-text-body-1`
- `#8a92a3`, `#7c8493`, `#8991a6` → `--color-text-body-3`
- `#f2f3f5`, `#f5f7fb`, `#f9f9f9` → `--color-surface-shade-1`
- `#e4e7ee`, `#d5def2`, `#e7eaf2` → `--color-border-default`

---

## 8. Open notes (from the designer)

- In dark, `--color-text-body-2` (`#e5e5e5`) comes out lighter than `body-1`, and `body-3` is the same as `body-1`. The ladder isn't final, so use only `body-1` and `body-3` for now.
- In dark, `surface-disabled`, `surface-warning-light` and `border-disabled` are still the light-mode values, and haven't been adjusted yet.
- The eyebrow color `#00b2e4` on white has low contrast (~2.6:1). It may get a darker light-mode value later.

Any new color or a change to a dark value happens **in Figma first**, and then gets copied into `route-dark-mode-tokens.css`.
