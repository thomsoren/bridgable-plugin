---
name: openbridge-topbar-menus
description: Topbar menu patterns for OpenBridge applications. Covers topbar slot anatomy, command menu, alert button, brilliance and palette menu, system settings, user menu, clock and calendar, application menu, and overflow context menu. Use when building or configuring topbar menus, dropdown panels, or topbar button areas.
---

# Topbar Menus

The OpenBridge topbar (`ObcTopBar`) is a 48px-high bar. Each menu drops down from its trigger button, positioned directly below the bar at `top: 52px`.

## Topbar Slot Anatomy

```
[Hamburger] [AppIcon] AppTitle PageName [CommandButton]  ...  [Alerts] [System] [Dimming] [User] [Clock] [Apps]
|_________________ left area ________________________|       |_________________ right area ___________________|
```

- **Left**: hamburger, optional app icon (`slot="app-icon"`), `appTitle`, `pageName`, command button (`slot="command-button"`)
- **Right** (left-to-right): alerts (`slot="alerts"`), system button, dimming button, user button, clock (`slot="clock"`), apps button
- Each right-area element has a `show*` boolean and `*Activated` boolean on `ObcTopBar`.

## ObcTopBar

```tsx
import { ObcTopBar } from "@oicl/openbridge-webcomponents-react/components/top-bar/top-bar.js";
```

Key props: `appTitle`, `pageName`, `showClock`, `showDimmingButton`, `showAppsButton`, `showUserButton`, `showAppIcon`, `showDate`, `tall`, `wideMenuButton`, `inactive`.
Slots: `app-icon`, `command-button`, `alerts`, `clock`.

## Command Menu

Displays propulsion/automation command status. Lets operators take/release command of vessel systems (DP, Joystick, IAS). Triggered from the command button in the left area.

**Variants**: Small (360px, no graphic) for simple take/release; Large (720px, with graphic sidebar) for propulsion or automation command with mode selection.

```tsx
import { ObcCommandMenu } from "@oicl/openbridge-webcomponents-react/components/command-menu/command-menu.js";
```

Props: `inCommand` (boolean), `showLocation` (boolean).
Events: `command-take`, `command-release`.

## Alert Button (Notifications)

Notification bell + silence button in the topbar right area. Displays alert counts with badge indicators.

```tsx
import { ObcAlertButton } from "@oicl/openbridge-webcomponents-react/components/alert-button/alert-button.js";
```

Props: `alertCount`, `showCount`, `alertStatus` (alarm | warning | caution | running | no-alert).
Place inside `slot="alerts"` on `ObcTopBar`.

## System Settings Menu

Centralized view of hardware/sensor status: Wi-Fi, audio/volume, microphone, battery. Triggered by the system button (cluster of status icons). The system button is built into `ObcTopBar` -- no standalone component. Build the dropdown panel with `ObcNavigationItem` rows, each section having a title and inline controls (toggle, slider).

## Brilliance & Palette Menu

Controls screen brightness and color palette. Two tabs: "Brilliance" (brightness slider + lumen value) and "Day/Night" (palette carousel: Day, Dusk, Night, Bright). Triggered by the dimming button (sun icon); set `showDimmingButton`.

```tsx
import { ObcBrillianceMenu } from "@oicl/openbridge-webcomponents-react/components/brilliance-menu/brilliance-menu.js";
```

Props: `palette` (`'day'` | `'dusk'` | `'night'` | `'bright'`), `brightness` (0-100), `showLinkBrightness`, `showLinkPalette`, `showScreenControlLink`.
Events: `brilliance-changed`, `palette-changed`.

## User Menu

Identity management: sign in/out with username/password fields, plus a "Recently signed in" section with user avatar buttons. Triggered by user button (person icon); set `showUserButton`. No dedicated component -- compose with text inputs and buttons in a 320px floating panel.

## Clock & Calendar

Displays time, date, timezone. Clicking opens a calendar. Set `showClock` (and `showDate`) on `ObcTopBar`.

```tsx
import { ObcClock } from "@oicl/openbridge-webcomponents-react/components/clock/clock.js";
```

Props: `date` (ISO string), `timeZoneOffsetHours`, `showDate`, `showSeconds`, `showTimeZone`, `activated` (true when calendar is open).
Place inside `slot="clock"`. **Colon animation**: the colon blinks on a 950ms loop cycle.

## Application Menu

Switches between entire applications (e.g., ECDIS to Conning) -- distinct from the hamburger which switches pages within an app. Triggered by apps button (grid icon); set `showAppsButton`. Contains a search field, grid of circular app buttons with labels, an "All apps" accordion, and a "Screen control" link. Build as a 360px floating panel.

## Overflow / Context Menu

On small or partial topbars, an overflow button collapses right-area items into a dropdown list.

```tsx
import { ObcContextMenu } from "@oicl/openbridge-webcomponents-react/components/context-menu/context-menu.js";
import { ObcNavigationItem } from "@oicl/openbridge-webcomponents-react/components/navigation-item/navigation-item.js";

<ObcContextMenu>
  <ObcNavigationItem label="Notifications" hasIcon>
    <obi-notification-advice-active slot="icon" />
  </ObcNavigationItem>
  <ObcNavigationItem label="System" hasIcon>
    <obi-system slot="icon" />
  </ObcNavigationItem>
</ObcContextMenu>
```

## Topbar Setup Example

```tsx
import { ObcTopBar } from "@oicl/openbridge-webcomponents-react/components/top-bar/top-bar.js";
import { ObcAlertButton } from "@oicl/openbridge-webcomponents-react/components/alert-button/alert-button.js";
import { ObcClock } from "@oicl/openbridge-webcomponents-react/components/clock/clock.js";

<ObcTopBar
  appTitle="Conning"
  pageName="Overview"
  showDimmingButton
  showClock
  showAppsButton
  showUserButton
>
  <ObcAlertButton slot="alerts" />
  <ObcClock slot="clock" date={new Date().toISOString()} showSeconds />
</ObcTopBar>
```

All menus open as floating panels with `border-radius: 12px` and floating drop shadow, positioned at `top: 52px`. Command menu aligns left; system/brilliance/user/apps menus align right to their trigger.
