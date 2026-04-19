---
name: bridgable-theming
description: OpenBridge color-token rules for any custom CSS that sits next to OpenBridge components. Load whenever the user writes hand-rolled CSS for text, backgrounds, borders, surfaces, or alert-state styling — and any time the user switches palettes (day, dusk, night, bright) or reports unreadable text in a dark theme. Framework-agnostic.
---

# Bridgable Theming

Use this skill whenever you (or the user) write CSS that lives **alongside**
OpenBridge components. OpenBridge ships its own palette via
`data-obc-theme="day|dusk|night|bright"` on `<html>`. Custom CSS that hardcodes
colors — or falls back to a hex when an undefined variable is referenced —
breaks the moment the palette flips.

## When to load

- The user adds a card, header, label, button, or any custom-styled wrapper
  around OpenBridge components.
- The user reports "text is unreadable in dusk/night" or "the theme switch
  doesn't change my text".
- The user is about to write CSS that touches `color`, `background`,
  `background-color`, `border`, `border-color`, `box-shadow`, or `outline`.
- The user asks how to style alert / out-of-range states.

## The token map

Use these exact CSS variables. **Never** add a literal hex fallback — if the
variable is undefined the browser falls back to the inherited value, which is
already correct under `data-obc-theme`. Hex fallbacks defeat the entire
palette-switching system.

### Text

| Use for | Token |
|---|---|
| Primary text (titles, values, body) | `var(--element-active-color)` |
| Secondary text (labels, captions, units) | `var(--element-neutral-color)` |
| Hint / placeholder / disabled | `var(--element-inactive-color)` |
| Disabled hard | `var(--element-disabled-color)` |

Inverted variants (`--element-active-inverted-color`, etc.) are for placing
text **on top of** a filled active surface.

### Surfaces

| Use for | Token |
|---|---|
| Card / panel background | `var(--container-background-color)` |
| App / page backdrop (the gap between cards) | `var(--container-backdrop-color)` |
| Secondary / nested surface | `var(--container-background-color-secondary)` |

### Borders

| Use for | Token |
|---|---|
| Card outline (subtle silhouette) | `var(--border-silhouette-color)` |
| Input outline / focus ring base | `var(--border-outline-color)` |
| Divider lines inside a card | `var(--border-silhouette-color)` |

### Alerts (out-of-range styling)

Match the IMO color taxonomy. Don't pick reds/yellows yourself.

| State | Surface / border | Text on top |
|---|---|---|
| Alarm | `var(--alert-alarm-color)` | `var(--on-alarm-color)` |
| Warning | `var(--alert-warning-color)` | `var(--on-warning-color)` |
| Caution | `var(--alert-caution-color)` | `var(--on-caution-color)` |
| Notification / info | `var(--alert-notification-color)` | `var(--on-notification-color)` |

For alert *border* on an otherwise-normal card (the common case), apply the
alert color to `border-color` and a matching `box-shadow` inset. Keep the card
background as `--container-background-color` so the content stays legible.

### Buttons (when wrapping flat clickable elements yourself)

| Use for | Token |
|---|---|
| Hover background | `var(--button-flat-hover-background-color)` |
| Active / pressed background | `var(--button-flat-active-background-color)` |
| Text on selected/active state | `var(--on-selected-active-color)` |

Prefer the actual `<obc-button>` / `ObcButton` component over hand-rolled
buttons whenever possible — it gets every state right for free.

## Anti-patterns to remove on sight

```css
/* ❌ never */
color: var(--text-color, #111);     /* no such token; hex always wins in dark themes */
color: #111;                         /* hard-locked to light theme */
background: #fff;
border: 1px solid rgba(0, 0, 0, 0.1);

/* ❌ never */
@media (prefers-color-scheme: dark) { ... }   /* OB theme is data-obc-theme, not OS prefs */

/* ❌ never */
:root { color-scheme: light dark; }           /* fights OB's color tokens */
```

```css
/* ✅ always */
color: var(--element-active-color);
background: var(--container-background-color);
border: 1px solid var(--border-silhouette-color);
```

## Required verification

Before reporting a theming-related task done, **cycle the palette** through
all four values and confirm every text/surface/border element changes
appropriately:

```js
for (const t of ['day', 'dusk', 'night', 'bright']) {
  document.documentElement.dataset.obcTheme = t;
  // visually confirm titles, values, captions, dividers, alert borders
}
```

If a label looks black-on-black in dusk, it's almost always a literal hex or
a `var(--text-color, #...)` fallback. Grep the project for `#` in CSS and for
fallback commas inside `var(...)` and replace each with the right token from
the map above.

## Component-internal CSS knobs (`--app-components-*`)

Many OpenBridge components ship with their own sizing/spacing tokens —
named `--app-components-<component>-<thing>` — that the component reads from
its **own** styles. Outer wrappers can't shrink the component with normal
CSS (`width: 100%`, `max-width: 320px`, etc.) because the component's
internal `width: var(--app-components-...)` wins.

The fix is to **override the var directly** on the component or one of its
ancestors:

```css
/* Floating notification is 640px by default; squeeze into a 340px column. */
.toasts obc-notification-floating-item {
  --app-components-floating-message-item-component-width: 100%;
}
```

These tokens are **not** returned by `bridgable_query`. To find them:

1. `grep -rh "var(--app-components-" node_modules/@oicl/openbridge-webcomponents/dist/components/<component>/` —
   that lists every override knob the component exposes.
2. Or open `<component>.css.js` in `dist/components/<component>/` directly.

Do this **before** wrapping a component in your own sizing CSS. Saves the
"why is my container being ignored?" debugging round-trip.

## Components have internal layout assumptions

A few components apply layout to their own contents (vertical centering,
fixed paddings, max-widths) that surprises you when you stretch them in a
parent grid. Most common: `obc-card`'s inner `.content` uses
`justify-content: center`, so a stretched card vertically centers its
slotted children instead of top-aligning them.

If a stretched component looks "wrong" (centered when you wanted
top-aligned, or padded when you wanted flush), the answer is almost always
in `<component>.css.js`. Read it before fighting the symptom with
`!important` or extra wrapper divs. CSS Parts (`::part(content)`) let you
override safely when the component exposes them — `bridgable_query` returns
the part list per component.

## Wiring the topbar dimming button to the palette

Most apps want the day/night toggle in the topbar to actually cycle the
palette. The standard pattern is to either show the brilliance menu on
click (preferred — that's what `bridgable_app_pattern("shell", ...)`
does) or cycle palettes inline:

```ts
// On dimming-button-clicked:
const order = ['day', 'dusk', 'night', 'bright'] as const;
const next = order[(order.indexOf(palette) + 1) % order.length];
document.documentElement.setAttribute('data-obc-theme', next);
localStorage.setItem('obc-palette', next);
```

If the user asks for theme switching but you're not loading the full app
shell, wire `dimming-button-clicked` to one of these patterns — don't
just leave `data-obc-theme` hardcoded.

## Setup precondition

These tokens only exist when `@oicl/openbridge-webcomponents/dist/openbridge.css`
is loaded **before** any custom CSS, and `<html data-obc-theme="...">` is set.
If either is missing, call `bridgable_setup` for the user's framework first —
it returns the correct boilerplate.
