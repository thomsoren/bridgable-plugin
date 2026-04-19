---
name: openbridge-page-templates
description: Standard page templates and window patterns for OpenBridge. Covers Help page, Settings page, and Company page (all sharing a sidebar plus content layout), floating draggable windows, and ObcModalWindow dialogs. Use when building settings pages, help sections, company info pages, confirmation dialogs, or overlay tool panels.
---

# Page Templates & Window Patterns

Standard pages (Help, Settings, Company) and overlay windows (floating, modal) in OpenBridge.

## Shared Page Structure

All three system pages share the same layout: **TopBar (settings mode) + left sidebar + toolbar + content area**.

```
+----------------------------------------------------------+
| TopBar (settings=true, breadcrumbs)                      |
+-------------+--------------------------------------------+
| Sidebar     | Toolbar (back/forward + breadcrumb path)   |
| (320px)     |--------------------------------------------|
| Navigation  | Content area (scrollable)                  |
| categories  |                                            |
+-------------+--------------------------------------------+
```

- **TopBar** enters settings mode (`settings` prop) with close/back/forward buttons and breadcrumbs.
- **Sidebar** (320px, border-right divider) lists page categories as `ObcNavigationItem` entries.
- **Toolbar** sits below the topbar with back/forward arrows and an `ObcBreadcrumb` path.
- **Content area** fills remaining space and scrolls independently.

### TopBar in Settings Mode

```tsx
import { ObcTopBar } from "@oicl/openbridge-webcomponents-react/components/top-bar/top-bar.js";

<ObcTopBar
  settings
  appTitle="My App"
  pageName="Settings"
  breadcrumbItems={[{ label: "Settings" }, { label: "Display" }, { label: "Theme" }]}
  showDimmingButton
  showClock
/>
```

Key props: `settings` (boolean), `breadcrumbItems` (BreadcrumbItem[]). Events: `close`, `back`, `forward`.

### Sidebar + Content Skeleton

```tsx
import { ObcNavigationItem } from "@oicl/openbridge-webcomponents-react/components/navigation-item/navigation-item.js";

function SystemPageLayout({ sidebarItems, children }) {
  return (
    <div style={{ display: "flex", flex: 1, overflow: "hidden" }}>
      <nav style={{ width: 320, borderRight: "1px solid var(--divider, #ddd)", overflowY: "auto" }}>
        {sidebarItems.map((item) => (
          <ObcNavigationItem key={item.label} label={item.label} hasIcon={!!item.icon} activated={item.active}>
            {item.icon && <span slot="icon">{item.icon}</span>}
          </ObcNavigationItem>
        ))}
      </nav>
      <main style={{ flex: 1, overflowY: "auto", padding: "48px 320px" }}>{children}</main>
    </div>
  );
}
```

## Help Page

Accessed from the **navigation menu footer** (bottom of main nav sidebar). Sidebar lists help categories (Getting Started, Troubleshooting, etc.). Content area displays articles, guides, or FAQ entries. Categories use `ObcNavigationItem` grouped into sections separated by dividers; the selected category uses `activated`.

## Settings Page

Accessed from the **topbar** or **navigation menu footer**. Sidebar groups settings (Display, Network, Audio). Content area uses form-style layouts with `ObcElevatedCard` for sections:

```tsx
import { ObcElevatedCard } from "@oicl/openbridge-webcomponents-react/components/elevated-card/elevated-card.js";

<ObcElevatedCard>
  <span slot="title">Display Settings</span>
  <span slot="description">Adjust brightness and theme</span>
</ObcElevatedCard>
```

## Company Page

Accessed from the **navigation menu** (main section). Content layout:
1. **Header banner** -- accent-colored background with company/vessel logo
2. **Company name + description** text block
3. **Elevated cards** for sections (Products, About Us, Contact) with action buttons and trailing chevron

Sidebar categories: Home, Products, About Us, Contact -- same `ObcNavigationItem` pattern.

## Floating Window

Draggable overlay on top of the main interface. Has its own `ObcTopBar` and content area. Users can move and resize it. Supports optional auto-close radial progress on the close button.

```tsx
<div style={{
  position: "absolute", width: 720, height: 560, borderRadius: 12,
  overflow: "hidden", boxShadow: "0px 4px 16px rgba(0,0,0,0.31)",
  background: "var(--color/container/background-color, #f7f7f7)",
}}>
  <ObcTopBar appTitle="App" pageName="Tool" />
  <div style={{ flex: 1, overflow: "auto" }}>{/* content */}</div>
</div>
```

**When to use:** Secondary tools or apps alongside the main view (calculator, reference chart, monitoring panel).

## Modal Window

Centered dialog with backdrop that blocks background interaction until dismissed.

```tsx
import { ObcModalWindow } from "@oicl/openbridge-webcomponents-react/components/modal-window/modal-window.js";

<ObcModalWindow size="medium" hasLeadingIcon hasOptionalAction>
  <span slot="leading-icon"><obi-info /></span>
  <span slot="title">Confirm Changes</span>
  <div slot="content"><p>Save your changes before closing?</p></div>
  <span slot="option-label">Discard</span>
  <span slot="cancel-label">Cancel</span>
  <span slot="done-label">Save</span>
</ObcModalWindow>
```

Props: `size` (`"small"` | `"medium"` | `"large"`), `hasOptionalAction`, `hasLeadingIcon`.
Events: `close-click`, `cancel-click`, `done-click`, `option-click`.

**When to use:** Confirmations, save/discard prompts, or any action requiring user response before continuing.

## Floating vs Modal -- Decision Guide

| Criteria | Floating Window | Modal Window |
|----------|----------------|--------------|
| Background interaction | Allowed | Blocked |
| Movable/resizable | Yes | No (centered) |
| Has own TopBar | Yes | No (title bar + close) |
| Use case | Secondary tools, reference panels | Confirmations, required input |
| Component | Custom `div` + `ObcTopBar` | `ObcModalWindow` |
