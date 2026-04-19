---
name: openbridge-alerts
description: Alert handling patterns for OpenBridge maritime applications. Covers the 3-tier alert flow (ObcAlertButton in topbar, ObcAlertMenu dropdown, full alert list page), alert color coding (alarm/warning/caution), ObcAlertMenuItem composition, and alert table layout. Use when building alert displays, alarm panels, notification indicators, or alert list views.
---

# Alert Handling Patterns

Three-tier system: **topbar button** (at-a-glance status) -> **dropdown menu** (quick triage) -> **full list page** (detailed management).

## Navigation Flow

1. `ObcAlertButton` in topbar shows alert count + severity color.
2. Click opens `ObcAlertMenu` -- floating 640px dropdown below topbar (app manages positioning).
3. Menu footer "Alert list" button navigates to full page (`ObcAlertListPageSmall` or custom `ObcAlertListDetails` layout).
4. Hamburger / navigation menu can also link directly to the alert list page.

## Components & Imports

All from `@oicl/openbridge-webcomponents-react`:

| Component | Import Path |
|-----------|-------------|
| `ObcAlertButton` | `components/alert-button/alert-button.js` |
| `ObcAlertMenu` | `components/alert-menu/alert-menu.js` |
| `ObcAlertMenuItem` | `components/alert-menu-item/alert-menu-item.js` |
| `ObcAlertIcon` | `components/alert-icon/alert-icon.js` |
| `ObcAlertListDetails` | `components/alert-list-details/alert-list-details.js` |
| `ObcAlertDetailPage` | `pages/alert-detail-page/alert-detail-page.js` |
| `ObcAlertListPageSmall` | `pages/alert-list-page-small/alert-list-page-small.js` |
| `ObcAlertList` | `building-blocks/alert-list/alert-list.js` |

## Alert Types & Color Coding

| Type | Color | Icon |
|------|-------|------|
| `alarm` | Red (`#e30019`) | Diamond with exclamation |
| `warning` | Orange/amber | Triangle with exclamation |
| `caution` | Yellow | Triangle outline |

Use `ObcAlertIcon` with `type` (`"alarm"` / `"warning"` / `"caution"`) and `active` / `acknowledged` props. Blinking applies to alarm and warning only.

## Topbar Alert Button

Props: `nAlerts` (number), `alertType` (`"alarm"` | `"warning"` | `"caution"` | undefined), `type` (`"flat"` | `"normal"` | `"enhanced"`), `counter` (boolean), `showSilenceButton`, `blinking`. Events: `click-alert`, `click-silence`.

```tsx
<ObcAlertButton
  nAlerts={5}
  alertType="alarm"
  type="enhanced"
  counter
  showSilenceButton
  blinking
  onClickAlert={() => setMenuOpen(true)}
  onClickSilence={handleSilence}
/>
```

## Alert Menu (Dropdown)

Tabbed panel: Unacked | Shelved | All alerts. Contains `ObcAlertMenuItem` children. Footer has "ACK all visible", "Silence", and "Alert list" buttons.

Props: `hasShelved`, `canAckAll`. Events: `ack-all-visible-click`, `silence-click`, `go-to-alert-list-click`.

```tsx
<ObcAlertMenu hasShelved canAckAll>
  <ObcAlertMenuItem
    status="unacknowledged"
    title="Engine Temperature High"
    description="Port main engine exceeds threshold"
    time="14:30"
    onAckClick={handleAck}
  >
    <ObcAlertIcon slot="alert-icon" type="alarm" active />
  </ObcAlertMenuItem>
</ObcAlertMenu>
```

**ObcAlertMenuItem** props: `status` (`"unacknowledged"` | `"caution"` | `"acknowledged"` | `"no-ack-alarm"` | `"no-ack-warning"` | `"rectified"`), `title`, `description`, `time`, `day`, `shelved`, `hasIcon`, `size` (`"single-line"` | `"multi-line"`). Slots: `alert-icon` (required), `icon` (optional source icon). Events: `ack-click`, `item-click`.

## Full Alert List Page Layout

```
+--------------------------------------------------+
| ObcTopBar (with ObcAlertButton)                  |
+--------------------------------------------------+
| ObcAlertListDetails (table)  | ObcAlertDetailPage |
|   Columns: Status, ACK-     |   (side panel)     |
|   status, Activated, Cat.,  |   Title, desc, tag |
|   Condition, Tag ID         |   ID, timestamps,  |
|                             |   readout graph,   |
|                             |   Shelf/ACK buttons|
+--------------------------------------------------+
| Toolbar: [Unacked|Shelved|All active|History|    |
|  Blocked] + filter icons + ACK all + Silence     |
+--------------------------------------------------+
```

**ObcAlertListDetails**: Props `alerts` (Alert[]), `selectedMode` (AlertListMode), `showTime`, `small`. Events: `ack-click`, `row-click`.

**ObcAlertDetailPage**: Props `alert` (Alert), `type` (AlertDetailPageType), plus boolean toggles: `hasNote`, `hasActions`, `hasTagId`, `hasCategory`, `hasActivated`, `hasTimer`, `hasAcknowledged`, `hasAcknowledgedBy`, `hasRectified`, `hasShelvingTimer`, `hasShelvedBy`, `hasReadoutGraph`.

**ObcAlertListPageSmall**: Self-contained page with toolbar + table. Props: `alerts`, `selectedMode`, `hasShelved`, `showTime`. Events: `ack-all-visible-click`, `ack-click`, `row-click`, `silence-click`.

## Composition Rules

- `ObcAlertMenu` children must be `ObcAlertMenuItem` elements.
- Each `ObcAlertMenuItem` requires an `ObcAlertIcon` in the `alert-icon` slot.
- The alert menu is **not** part of the topbar -- the app positions it absolutely below the topbar on alert button click.
- For custom list layouts, compose `ObcAlertListDetails` + toolbar manually. For simple cases, use `ObcAlertListPageSmall`.
- Alert data shape: `{ id, tagId, source, text, acknowledged, active, type, time, noAck? }`.
