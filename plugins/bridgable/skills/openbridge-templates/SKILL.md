---
name: openbridge-templates
description: Page shell structure and template variants for OpenBridge applications. Covers ObcTopBar, ObcNavigationMenu, content area layout, and 6 template types (Regular, Tall, Double, Split, Inactive, Small). Use when building full-page layouts, app shells, or configuring the basic page structure with topbar and navigation.
---

# OpenBridge Template Patterns

## Imports

```tsx
import { ObcTopBar } from "@oicl/openbridge-webcomponents-react/components/top-bar/top-bar.js";
import { ObcNavigationMenu } from "@oicl/openbridge-webcomponents-react/components/navigation-menu/navigation-menu.js";
import { ObcNavigationItem } from "@oicl/openbridge-webcomponents-react/components/navigation-item/navigation-item.js";
import { ObcNavigationItemGroup } from "@oicl/openbridge-webcomponents-react/components/navigation-item-group/navigation-item-group.js";
import { ObcClock } from "@oicl/openbridge-webcomponents-react/components/clock/clock.js";
import { ObcAlertButton } from "@oicl/openbridge-webcomponents-react/components/alert-button/alert-button.js";
```

## Page Shell Structure

Every page has: **TopBar** (fixed top), **NavigationMenu** (left), **content area** (fills rest). Backdrop is `#e0e0e0`.

```tsx
export default function AppLayout({ children }: { children: React.ReactNode }) {
  return (
    <div style={{ width: "100%", height: "100vh", position: "relative", background: "#e0e0e0" }}>
      <ObcTopBar appTitle="Bridge" pageName="Overview" showClock showDimmingButton showAppIcon>
        <obi-ship slot="app-icon" />
        <ObcClock slot="clock" />
        <ObcAlertButton slot="alerts" />
      </ObcTopBar>
      <div style={{ position: "absolute", top: 48, bottom: 0, left: 0, right: 0, display: "flex" }}>
        <ObcNavigationMenu variant="icon-only-large">
          <ObcNavigationItem slot="main" label="Overview" hasIcon checked>
            <obi-placeholder slot="icon" />
          </ObcNavigationItem>
          <ObcNavigationItem slot="footer" label="Settings" hasIcon>
            <obi-settings-iec slot="icon" />
          </ObcNavigationItem>
        </ObcNavigationMenu>
        <main style={{ flex: 1, overflow: "auto" }}>{children}</main>
      </div>
    </div>
  );
}
```

## Template Variants

| Variant | TopBar | Content Top | Key Difference |
|---------|--------|-------------|----------------|
| **Regular** | 48px `<ObcTopBar>` | 48px | Default single-view layout with rail nav |
| **Tall** | 64px `<ObcTopBar tall>` | 64px | Larger touch targets; clock stacks time/date vertically |
| **Double** | 96px | 96px | Second row below TopBar with notification + alert message strips |
| **Split** | 48px | 48px | No nav menu; two fixed 320px side panels + center content |
| **Inactive** | 48px `<ObcTopBar inactive>` | 48px | Muted non-interactive state (locked/modal) |
| **Small** | 48px | 48px | Narrow screens (~489px); compact TopBar hides date |

**Split layout** has no NavigationMenu. TopBar splits into left/right 320px sections. Content: left panel `0-320px`, center `320px to calc(100%-320px)`, right panel last `320px`.

**Double layout** top row is the standard TopBar; bottom 48px row has two halves for notification messages (left) and alert messages (right), each with their own action button.

## Navigation Patterns

| Pattern | Variant Prop | Width | Use When |
|---------|-------------|-------|----------|
| **Rail** | `icon-only-large` | ~56px | Default for most apps; icons only with flyout support |
| **Full sidebar** | `full` | 320px | Menu open state; shows icon + label per item |
| **Compact** | `compact` | narrow | Space-constrained layouts |

**Rule:** Use `icon-only-large` (not `icon-only`) when nav has groups or flyouts.

Flyout navigation uses `<ObcNavigationItemGroup>` to create expandable sub-menus from the rail:
```tsx
<ObcNavigationItemGroup slot="main" label="Apps">
  <obi-applications slot="icon" />
  <ObcNavigationItem label="Sub item 1" hasIcon><obi-placeholder slot="icon" /></ObcNavigationItem>
</ObcNavigationItemGroup>
```

## ObcTopBar API

| Prop | Type | Purpose |
|------|------|---------|
| `appTitle` | string | App name after hamburger icon |
| `pageName` | string | Bold page name after app title |
| `tall` | boolean | 64px height |
| `inactive` | boolean | Muted non-interactive state |
| `showClock` | boolean | Show clock slot |
| `showDate` | boolean | Date alongside clock |
| `showDimmingButton` | boolean | Day/night toggle |
| `showUserButton` | boolean | User/profile icon |
| `showAppsButton` | boolean | App grid button |
| `showAppIcon` | boolean | Show app-icon slot |
| `wideMenuButton` | boolean | Wide hamburger for rail-labeled nav |

**Slots:** `app-icon`, `command-button`, `alerts`, `clock`
**Events:** `menu-button-clicked`, `dimming-button-clicked`, `user-button-clicked`, `apps-button-clicked`

## ObcNavigationMenu API

| Prop | Type | Values |
|------|------|--------|
| `variant` | string | `full`, `icon-only`, `icon-only-large`, `compact` |
| `smallScreen` | boolean | Responsive footer/logo layout |

**Slots:** `main` (primary items), `footer` (settings/help/alerts), `logo` (vendor branding via `<ObcVendorButton>`)

## ObcNavigationItem API

Props: `label` (string), `checked` (boolean -- active/selected blue highlight), `hasIcon` (boolean), `href` (string).
**Slot:** `icon` -- place OpenBridge icon (e.g., `<obi-settings-iec />`, `<obi-alert-list />`)

## Small Screen Rules

- TopBar: 48px, hides date, shows time only
- Nav opens as 320px overlay below TopBar (not persistent)
- Footer items become horizontal icon row at nav panel bottom
- Alert button: flat icon-only (no silence split button)
- Activate with `<ObcNavigationMenu smallScreen>`
