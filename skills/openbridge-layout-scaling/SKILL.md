---
name: openbridge-layout-scaling
description: Grid system, spacing rules, scaling formulas, and color palette management for OpenBridge bridge applications. Covers 8px grid baseline, 6-column layout at 320px columns, reading distance scaling (base 1920x1080 at 0.75m), and 4 color palettes (day/dusk/night/bright) with data-obc-theme switching. Use when setting up page grids, card layouts, responsive sizing for bridge screens, or implementing theme and palette controls.
---

# Layout, Grids, Scaling & Colour Palettes

Rules for building OpenBridge application layouts in Next.js. All values from the OpenBridge Application Patterns Figma file.

---

## 8px Grid System

All spacing, padding, margins, and component sizes MUST be multiples of **8px**.

```css
--spacing-xs: 8px;  --spacing-sm: 16px;  --spacing-md: 24px;
--spacing-lg: 32px;  --spacing-xl: 48px;
```

---

## 6-Column Grid

Base layout: **6 columns, no gutters**. At 1920px base, each column = **320px**.

- Content starts below the top bar (48px height)
- Cards snap to column boundaries
- Navigation rail present: columns redistribute within remaining space

```css
.app-content {
  display: grid;
  grid-template-columns: repeat(6, 1fr);
  width: 100%;
  height: calc(100% - 48px); /* below top bar */
}
```

### With Navigation Rail

```css
.app-shell {
  display: grid;
  grid-template-columns: 56px 1fr; /* rail + content */
  height: 100%;
}
.app-content {
  display: grid;
  grid-template-columns: repeat(6, 1fr);
}
```

Navigation menu widths: **320px** (full, 1 grid column) or **56px** (rail). Use `variant="icon-only"` for rail mode.

---

## Card Layout

Cards sit within grid columns with 4px internal padding. Common arrangements at 1920x1080:

| Layout area       | Columns | Width at base |
|-------------------|---------|---------------|
| Sidebar card      | 1       | 320px         |
| Main content card | 4       | 1280px        |
| Right panel card  | 1       | 320px         |

Cards subdivide vertically within a column (e.g. 3 stacked cards in a sidebar column).

### React Imports

```tsx
import { ObcCard } from "@oicl/openbridge-webcomponents-react/components/card/card.js";
import { ObcTopBar } from "@oicl/openbridge-webcomponents-react/components/top-bar/top-bar.js";
import { ObcNavigationMenu } from "@oicl/openbridge-webcomponents-react/components/navigation-menu/navigation-menu.js";
```

---

## Base Screen & Reading Distance

Reference target: **27" screen at 0.75m** = **1920x1080** (1 mm = 3.2 px).

### Scaling Formula

```
SF = 0.75 / actual_distance_in_meters
Width  = 1920 * SF
Height = 1080 * SF
```

### Examples (27" screen)

| Distance | SF    | Resolution  | Effect                  |
|----------|-------|-------------|-------------------------|
| 0.75 m   | 1.0   | 1920 x 1080 | Base (full desktop)     |
| 1 m      | 0.75  | 1440 x 810  | Reduced real estate     |
| 1.5 m    | 0.5   | 960 x 540   | Significant reduction   |
| 2 m      | 0.375 | 720 x 405   | Small-screen equivalent |

**Why this matters:** Bridge screens are viewed from 1-2m. Components (text, icons, touch targets) must stay readable at distance. Design at the scaled resolution so OpenBridge components remain within maritime regulation sizes.

---

## Colour Palettes

4 palettes controlled via `data-obc-theme` on `<html>`:

| Palette    | Value    | Use case                                         |
|------------|----------|--------------------------------------------------|
| **Day**    | `day`    | Default. Normal indoor lighting                  |
| **Dusk**   | `dusk`   | Dark mode, less light pollution than Day          |
| **Night**  | `night`  | Minimal light, preserves night vision on bridge   |
| **Bright** | `bright` | High contrast for extreme brightness (Arctic)     |

### Switching Palettes

```html
<html lang="en" data-obc-theme="day">
```

```ts
document.documentElement.setAttribute("data-obc-theme", "night");
```

### Brilliance Menu Component

```tsx
import { ObcBrillianceMenu } from "@oicl/openbridge-webcomponents-react/components/brilliance-menu/brilliance-menu.js";

<ObcBrillianceMenu palette="day" brightness={75} showBrightness />
```

Props: `palette` (`"day" | "dusk" | "night" | "bright"`), `brightness` (0-100), `showBrightness`, `showLinkPalette`, `showLinkBrightness`.
Events: `palette-changed`, `brightness-changed`.
