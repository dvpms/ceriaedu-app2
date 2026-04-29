---
inclusion: always
---

# CeriaEdu Design System — Playful Learning

## Design Tokens

### Colors

```js
// tailwind.config.js / @theme
colors: {
  surface: '#f4fafd',
  'surface-dim': '#d4dbdd',
  'surface-bright': '#f4fafd',
  'surface-container-lowest': '#ffffff',
  'surface-container-low': '#eef5f7',
  'surface-container': '#e8eff1',
  'surface-container-high': '#e2e9ec',
  'surface-container-highest': '#dde4e6',
  'on-surface': '#161d1f',
  'on-surface-variant': '#414751',
  'inverse-surface': '#2b3234',
  'inverse-on-surface': '#ebf2f4',
  outline: '#717783',
  'outline-variant': '#c1c7d3',
  'surface-tint': '#0060ac',
  primary: '#005da7',
  'on-primary': '#ffffff',
  'primary-container': '#2976c7',
  'on-primary-container': '#fdfcff',
  'inverse-primary': '#a4c9ff',
  secondary: '#006d36',
  'on-secondary': '#ffffff',
  'secondary-container': '#83fba5',
  'on-secondary-container': '#00743a',
  tertiary: '#8b4c00',
  'on-tertiary': '#ffffff',
  'tertiary-container': '#af6100',
  'on-tertiary-container': '#fffbff',
  error: '#ba1a1a',
  'on-error': '#ffffff',
  'error-container': '#ffdad6',
  'on-error-container': '#93000a',
  'primary-fixed': '#d4e3ff',
  'primary-fixed-dim': '#a4c9ff',
  'on-primary-fixed': '#001c39',
  'on-primary-fixed-variant': '#004883',
  'secondary-fixed': '#83fba5',
  'secondary-fixed-dim': '#66dd8b',
  'on-secondary-fixed': '#00210c',
  'on-secondary-fixed-variant': '#005227',
  'tertiary-fixed': '#ffdcc2',
  'tertiary-fixed-dim': '#ffb77a',
  'on-tertiary-fixed': '#2e1500',
  'on-tertiary-fixed-variant': '#6d3a00',
  background: '#f4fafd',
  'on-background': '#161d1f',
  'surface-variant': '#dde4e6',
}
```

### Typography — Lexend

All text uses **Lexend** font (reduces visual stress, improves reading for children).

| Token | Size | Weight | Line Height |
|---|---|---|---|
| `headline-xl` | 40px | 700 | 1.2 |
| `headline-lg` | 32px | 700 | 1.2 |
| `headline-md` | 24px | 600 | 1.3 |
| `body-lg` | 18px | 400 | 1.6 |
| `body-md` | 16px | 400 | 1.6 |
| `label-md` | 14px | 600 | 1.2 / 0.02em spacing |
| `label-sm` | 12px | 500 | 1.2 |

### Border Radius

| Token | Value |
|---|---|
| `rounded-sm` | 0.25rem |
| `rounded` | 0.5rem |
| `rounded-md` | 0.75rem |
| `rounded-lg` | 1rem |
| `rounded-xl` | 1.5rem |
| `rounded-full` | 9999px |

Standard for all containers: **`rounded-xl` (12px equivalent)**. Pill shape (`rounded-full`) only for chips/tags/badges.

### Spacing (4px base unit)

| Token | Value | Usage |
|---|---|---|
| `xs` | 8px | Internal padding in small components |
| `sm` | 16px | Gap between related items |
| `md` | 24px | Primary gutter, card padding |
| `lg` | 32px | Section breaks |
| `xl` | 48px | Major layout shifts |

---

## Brand Philosophy

**"Gentle Guidance"** — balances playground energy with classroom clarity. Safe, approachable, encouraging for children while maintaining educational structure.

Visual style: **Modern Tactile** — generous whitespace, soft-depth elements, no sharp corners or harsh contrasts. Every interaction feels like a physical "nudge."

---

## Color Usage Rules

- **Primary blue (`#005da7`)**: Structural elements — headers, navigation, focus states
- **Secondary green (`#006d36` / `secondary-container: #83fba5`)**: Primary CTAs, "Go" states, progress, achievement
- **Tertiary orange (`#8b4c00` / `tertiary-container: #af6100`)**: Highlights, badges, "New" tags — attention without danger
- **Background (`#f4fafd`)**: Non-glare canvas
- **White (`surface-container-lowest`)**: Content containers / interaction zones

---

## Elevation & Shadows

```css
/* Level 1 — Default cards */
box-shadow: 0 4px 12px rgba(0, 93, 167, 0.08);

/* Level 2 — Hover / Active */
box-shadow: 0 6px 16px rgba(0, 93, 167, 0.14);
```

Max 3 layers of depth. Higher elevation = more "clickable."

---

## Component Standards

### Buttons
- Primary CTA: `secondary-container` green background, `rounded-xl`, font weight 600
- On press: `scale-[0.98]` for tactile feedback (use Framer Motion `whileTap`)
- Min touch target: **48px height**

### Cards
- Background: `surface-container-lowest` (white)
- Radius: `rounded-xl`
- Padding: `p-6` (24px / spacing-md)
- Shadow: Level 1

### Input Fields
- Border: 2px `outline-variant`, transitions to `primary` on focus
- Min height: **48px** (child-friendly touch target)
- Radius: `rounded-xl`

### Chips & Tags
- Shape: `rounded-full` (pill) — distinguishes from buttons
- Color: `tertiary-container` for highlights/badges

### Navigation (Student)
- Mobile: bottom bar, `surface-container-lowest` background
- Desktop: top bar, `primary` blue background, white icons/text

### Progress Bars
- Fill: `secondary-container` green
- Track: `surface-container-high`

---

## Layout

- Desktop: centered container, max-width 1200px
- Mobile: fluid, full-width
- All spacing aligned to **4px baseline grid**
- Gutter: 24px, Margin: 24px

---

## Accessibility Rules

- All interactive elements: min tap target **44×44px** (48px preferred)
- Text contrast: always use `on-*` token paired with its surface (e.g., `on-primary` on `primary`)
- Never use color alone to convey meaning — pair with icon or label
