---
name: openbridge-core
description: Core rules and conventions for building OpenBridge maritime web components in Next.js using the React wrapper package. Covers import path patterns, component naming, font setup, HTML theme attributes, AR 2D constraints, and preview sizing. Always loaded as the foundation for all OpenBridge development.
---

# OpenBridge Core Rules

**Component APIs (props, events, slots) come from the `lookup_component` tool, not this file.** This file only contains usage rules and design judgment that the tool cannot provide.

---

## Design playbook

When the task is open-ended ("build a dashboard for X", "show the operator Y"), don't just look up keywords. Decide what the user actually needs first.

**Monitoring screens (engines, tanks, generators):**
- Use `ObcInstrumentField` or `ObcMainEngine` for numeric readouts with units and limits
- Group related metrics in cards (`ObcCard`, `ObcElevatedCard`)
- For gauges that show "is this in the safe range", prefer instrument components with built-in alarm thresholds over plain numbers
- Layout: most-critical info top-left, scan-pattern matters under stress

**Control screens (valves, pumps, thrusters):**
- Use `ObcAutomationButton` with the appropriate icon for binary controls (open/close, on/off)
- Use `ObcSlider` or `ObcSliderDouble` for continuous adjustment
- For positional controls (rudder, thruster angle) use the dedicated component (`ObcRudder`, `ObcAzimuthThruster`)
- State should be visually obvious without reading text labels

**Process diagrams (P&ID, fuel systems, cooling loops):**
- Components on a 24px grid (see openbridge-automation skill)
- Tanks → valves → pumps → valves → tanks, connected by `ObcHorizontalLine` / `ObcVerticalLine` / `ObcCornerLine`
- Use `ObcAnalogValve` for continuous, `ObcDigitalValve` for binary, `ObcPump` for pumps
- Lines need a `medium` prop (oil/water/air/empty) — split the line at each valve

**Navigation views (heading, speed, position):**
- `ObcCompass` for prominent heading display, `ObcCompassFlat` for compact strip
- `ObcRudder` for rudder angle (semi-circular)
- `ObcInstrumentField` for speed, depth, course numbers
- These are all sized — give them an explicit container width/height (256px+ minimum)

**Forms and settings:**
- `ObcTextInputField` for text, `ObcNumberInputField` for numbers
- `ObcToggleSwitch` for on/off, `ObcToggleButtonGroup` + `ObcToggleButtonOption` for "pick one of N"
- `ObcSlider` for continuous values
- `ObcButton` is the standard button; label is children, not a prop: `<ObcButton>Save</ObcButton>`

**Alerts and notifications:**
- `ObcAlertButton` in topbar for at-a-glance count
- `ObcAlertMenu` + `ObcAlertMenuItem` for the dropdown panel
- `ObcAlertListDetails` for full alarm tables
- Notifications (`ObcNotificationMessageItem`) are different from alerts — use them for non-critical info

**When in doubt:** look up multiple candidates with `lookup_component`, read the descriptions, pick the most semantically specific one. `ObcRudder` beats `ObcInstrumentField` for rudder angle even though both could "show a number".

---

## What is OpenBridge?

OpenBridge is a design system and component library for **maritime**, **automation**, **AR (augmented reality)**, and **application** interfaces. Components use the `obc-*` tag prefix. Icons use the `obi-*` prefix.

---

## Package & Import Paths

**CRITICAL: Always use the React wrapper package for imports.**

React wrapper package: `@oicl/openbridge-webcomponents-react`
CSS package (for styles only): `@oicl/openbridge-webcomponents`

### Import Pattern

```tsx
import { ObcComponentName } from "@oicl/openbridge-webcomponents-react/<group>/<component>/<component>.js";
```

**NEVER** use vanilla JS imports like `@oicl/openbridge-webcomponents/dist/components/...`. Always use the React wrapper package.

**Note:** `valve-analoge-two-way-icon` has a typo in the folder name; the file is `valve-analog-two-way-icon.js`.

---

## Font: Noto Sans

OpenBridge uses **Noto Sans** as the primary font. Include it in HTML:

```html
<link href="https://fonts.googleapis.com/css2?family=Noto+Sans:wght@400;500;700&display=swap" rel="stylesheet">
```

---

## HTML Setup

```html
<html lang="en" data-obc-theme="day">
  <body class="obc-component-size-regular">
```

- `data-obc-theme`: `"day"` or `"night"`
- `obc-component-size-regular`: standard component sizing

---

## OpenBridge AR Framework (What It Is and Is NOT)

The OpenBridge AR framework is a set of specialized web components for **2D overlays** — spatial data, POIs, and markers on video/camera feeds or screen-space layouts.

**Do NOT mention Three.js, WebXR, or WebGL** when describing OpenBridge AR. It does not use 3D rendering. It works with 2D screen coordinates (x, y, boxWidth, boxHeight) and DOM overlays.

---

## AR POI: Specialized vs Building-Block Components

**Prefer specialized components** (`ObcPoiVessel`, `ObcPoiAton`) over the generic building-block pattern (`ObcPoiData` + `ObcPoi` + `ObcPoiObjectVessel`).

Do not use `ObcPoiController` in a normal preview scene unless the user explicitly asks for a controller-based or media-synced example.

---

## Preview Safety Notes

- Some navigation instruments measure their parent box at runtime. For previews, give them an explicit container size.
- Example: `ObcAzimuthThruster` should be rendered inside a sized wrapper such as `width: 512px; height: 512px`.
- Do not place watch-style instruments directly in an auto-sized parent if the preview scene does not otherwise establish width and height.

For preview composition, center standalone components by default so the user can inspect them easily. Do not center global or context-dependent components such as top bars or navigation shells.
