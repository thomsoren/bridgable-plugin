---
name: openbridge-navigation
description: Navigation menu patterns and interface anatomy for OpenBridge. Covers all 6 navigation variants (Full sidebar, Flyout, Tree, Rail, Rail-Labeled, Rail-Flyout), grouped pages with ObcNavigationItemGroup, and active page indication. Use when building navigation menus, sidebar navigation, or choosing between navigation layouts.
---

# Page Navigation & Interface Template

## Interface Template Anatomy

Every OpenBridge application follows a two-zone layout:

1. **Topbar** (zone 1) -- fixed at the top, 48px height. Contains menu button, app icon, app title, page name, alerts, system buttons, dimming, user, and clock.
2. **Content area** (zone 2) -- everything below the topbar. When the navigation menu is open, it sits to the left of the content area, pushing or overlaying content.

```
+--------------------------------------------------+
| [=] [icon] App  PageName        [alerts] [clock] |  <- Topbar (48px)
+------+-------------------------------------------+
| Nav  |                                           |
| Menu |        Content Area                       |
|      |                                           |
+------+-------------------------------------------+
```

## Components & Imports

```tsx
import { ObcTopBar } from "@oicl/openbridge-webcomponents-react/components/top-bar/top-bar.js";
import { ObcNavigationMenu } from "@oicl/openbridge-webcomponents-react/components/navigation-menu/navigation-menu.js";
import { ObcNavigationItem } from "@oicl/openbridge-webcomponents-react/components/navigation-item/navigation-item.js";
import { ObcNavigationItemGroup } from "@oicl/openbridge-webcomponents-react/components/navigation-item-group/navigation-item-group.js";
```

## Navigation Menu Variations

### 1. Full Height (320px wide)
Default sidebar. Shows icon + label for each page. Good for simple, flat page structures with few pages. Hidden when menu button is inactive -- uses no screen space when closed.

### 2. Full Height - Flyout
For deeper page structures. Groups expand into flyout panels beside the menu. Use `ObcNavigationItemGroup` to nest pages under group headings.

### 3. Full Height - Tree Navigation
Alternative to flyout. Groups expand inline (tree-style indentation). Pages nest under collapsible group rows within the same column.

### 4. Rail (48px wide, icon-only)
Always-visible icon strip. Best for simple structures with few pages and frequent page switching. Expands to full menu on menu button click. Use `variant="icon-only"` (no groups) or `variant="icon-only-large"` (with flyout groups).

### 5. Rail Labeled (64px wide)
Like Rail but includes short labels below icons. More identifiable at the cost of 16px extra width. Expands to full menu on click.

### 6. Rail - Flyout
Always-visible rail with flyout support. Hovering/clicking a group icon opens a floating panel with sub-pages. Best for deep structures with frequent navigation. Use `variant="icon-only-large"`.

## ObcNavigationMenu Props

| Prop | Type | Values |
|------|------|--------|
| `variant` | ObcNavigationMenuVariant | `"full"`, `"icon-only"`, `"icon-only-large"`, `"compact"` |
| `flyoutVariant` | ObcNavigationMenuFlyoutVariant | `"full"`, `"compact"` |
| `smallScreen` | boolean | Adapt layout for small viewports |

Slots: `main` (primary pages), `footer` (alerts, help, settings), `logo` (vendor branding).

## ObcNavigationItem Props

| Prop | Type | Purpose |
|------|------|---------|
| `label` | string | Page name |
| `href` | string | Navigation URL (renders as link) |
| `checked` | boolean | Active/selected page indicator |
| `hasIcon` | boolean | Show leading icon slot |
| `group` | boolean | Display as group item with flyout indicator |
| `groupSelected` | boolean | Highlight as selected within a group |

Slot: `icon` -- place an OpenBridge icon component (e.g. `<obi-placeholder>`).

## ObcNavigationItemGroup Props

| Prop | Type | Purpose |
|------|------|---------|
| `label` | string | Group heading text |
| `checked` | boolean | Group is active/selected |
| `hug` | boolean | Compact flyout anchored tightly to label |

Slots: `icon` (group icon), default slot (child `ObcNavigationItem` elements in the flyout).

## Code Pattern: App Shell with Grouped Navigation

```tsx
"use client";
import { useState } from "react";
import { ObcTopBar } from "@oicl/openbridge-webcomponents-react/components/top-bar/top-bar.js";
import { ObcNavigationMenu } from "@oicl/openbridge-webcomponents-react/components/navigation-menu/navigation-menu.js";
import { ObcNavigationItem } from "@oicl/openbridge-webcomponents-react/components/navigation-item/navigation-item.js";
import { ObcNavigationItemGroup } from "@oicl/openbridge-webcomponents-react/components/navigation-item-group/navigation-item-group.js";

export default function AppShell({ children }: { children: React.ReactNode }) {
  const [menuOpen, setMenuOpen] = useState(false);

  return (
    <div style={{ display: "flex", flexDirection: "column", height: "100vh" }}>
      <ObcTopBar
        appTitle="MyApp"
        pageName="Overview"
        menuButtonActivated={menuOpen}
        onMenuButtonClicked={() => setMenuOpen(!menuOpen)}
        showClock
      />
      <div style={{ display: "flex", flex: 1, overflow: "hidden" }}>
        {menuOpen && (
          <ObcNavigationMenu variant="full">
            <ObcNavigationItem slot="main" label="Dashboard" checked hasIcon>
              <obi-placeholder slot="icon" />
            </ObcNavigationItem>
            <ObcNavigationItemGroup slot="main" label="Operations" hasIcon>
              <obi-placeholder slot="icon" />
              <ObcNavigationItem label="Voyage" hasIcon>
                <obi-placeholder slot="icon" />
              </ObcNavigationItem>
              <ObcNavigationItem label="Cargo" hasIcon>
                <obi-placeholder slot="icon" />
              </ObcNavigationItem>
            </ObcNavigationItemGroup>
            <ObcNavigationItem slot="footer" label="Settings" hasIcon>
              <obi-settings-iec slot="icon" />
            </ObcNavigationItem>
          </ObcNavigationMenu>
        )}
        <main style={{ flex: 1, overflow: "auto" }}>{children}</main>
      </div>
    </div>
  );
}
```

## When to Use Each Variant

| Variant | Pages | Depth | Always visible | Width |
|---------|-------|-------|----------------|-------|
| Full height | Few | Flat | No | 320px |
| Full - Flyout | Many | Grouped | No | 320px + flyout |
| Full - Tree | Many | Grouped | No | 320px |
| Rail | Few | Flat | Yes | 48px |
| Rail labeled | Few | Flat | Yes | 64px |
| Rail - Flyout | Many | Grouped | Yes | 48px + flyout |
