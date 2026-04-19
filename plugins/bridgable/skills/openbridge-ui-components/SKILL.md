---
name: openbridge-ui-components
description: Core UI components for OpenBridge applications. Covers buttons (ObcButton variants normal/raised/flat), badges (ObcBadge, ObcNotificationButton), tooltips (ObcTooltip), dividers (ObcDivider), notification messages (ObcNotificationMessage, ObcNotificationMenuItem), toggles (ObcToggle), and input controls. Use when building forms, interactive controls, notification displays, or any standard UI elements.
---

# OpenBridge UI Components

## Import Table

Base: `@oicl/openbridge-webcomponents-react/components/`

| React name | Path suffix |
|---|---|
| `ObcButton` | `button/button.js` |
| `ObcIconButton` | `icon-button/icon-button.js` |
| `ObcAppButton` | `app-button/app-button.js` |
| `ObcBadge` | `badge/badge.js` |
| `ObcNotificationButton` | `notification-button/notification-button.js` |
| `ObcTooltip` | `tooltip/tooltip.js` |
| `ObcToggletip` | `toggletip/toggletip.js` |
| `ObcDivider` | `divider/divider.js` |
| `ObcNotificationMessageItem` | `notification-message-item/notification-message-item.js` |
| `ObcNotificationFloatingItem` | `notification-floating-item/notification-floating-item.js` |
| `ObcNotificationMenuItem` | `notification-menu-item/notification-menu-item.js` |
| `ObcToggleSwitch` | `toggle-switch/toggle-switch.js` |
| `ObcTextInputField` | `text-input-field/text-input-field.js` |
| `ObcNumberInputField` | `number-input-field/number-input-field.js` |
| `ObcCard` | `card/card.js` |
| `ObcElevatedCard` | `elevated-card/elevated-card.js` |

---

## Buttons — LABEL IS CHILDREN, NOT A PROP

`ObcButton` label goes in the **default slot** (React children). There is NO `label` prop.

Variants: `"normal"` (bordered, default), `"raised"` (filled/primary), `"flat"` (minimal).
Props: `variant`, `fullWidth`, `disabled`, `showLeadingIcon`, `showTrailingIcon`, `href`, `target`, `segmentPosition` (`"start"` | `"middle"` | `"end"`).

`ObcIconButton` — icon-only, icon in default slot. Same variants plus: `activated`, `cornerLeft`, `cornerRight`, `wide`, `progress` (0-100). Optional `hasLabel` with label slot.

```tsx
<ObcButton variant="raised">Confirm</ObcButton>
<ObcButton variant="normal" showLeadingIcon>
  <obi-download slot="leading-icon" />Download
</ObcButton>
<ObcIconButton variant="flat"><obi-settings /></ObcIconButton>
{/* Segmented button group */}
<div style={{ display: "flex" }}>
  <ObcButton segmentPosition="start">Left</ObcButton>
  <ObcButton segmentPosition="middle">Center</ObcButton>
  <ObcButton segmentPosition="end">Right</ObcButton>
</div>
```

---

## Badge & Notification Button

`ObcBadge` — count/status indicator. `number`, `type` (`"regular"` | `"alarm"` | `"warning"` | `"caution"` | `"running"` | `"notification"` | `"enhance"` | `"automation"` | `"outline"` | `"empty"`), `variant` (`"default"` | `"flat"`), `size` (`"regular"` | `"large"`), `showIcon`, `showNumber`. Custom icon via `badge-icon` slot.

`ObcNotificationButton` — bell with counter for top bars. `count`, `showCount`, `isActive`, `buttonStyle` (`"flat"` | `"normal"` | `"enhanced"`). Event: `obc-click`.

```tsx
<ObcBadge number={3} type="alarm" showIcon />
<ObcNotificationButton count={5} showCount isActive buttonStyle="normal" />
```

---

## Tooltip, Toggletip & Divider

`ObcTooltip` — the visual tooltip element itself, NOT a wrapper around a target. You must position it. Props: `label`, `type` (`"label"` | `"icon"`), `showIcon`. Icon via `icon` slot.

`ObcToggletip` — rich click-triggered popover. `title`, `description`, `hasActions`, `primaryButtonLabel`, `secondaryButtonLabel`. Events: `primary-action`, `secondary-action`.

`ObcDivider` — no props. Horizontal by default. For vertical, set `style={{ height: "100%" }}` with flex parent.

```tsx
<ObcTooltip label="Speed over ground" type="label" />
<ObcToggletip title="Info" description="Details" hasActions primaryButtonLabel="OK" />
<ObcDivider />
```

---

## Notification Components

Three types — NOT alerts (see openbridge-alerts skill). **FloatingItem uses slots; the other two use props.**

| Component | Context | Content via |
|---|---|---|
| `ObcNotificationMessageItem` | Inline banner | **Props**: `title`, `description`, `time`, `type`, `actionLabel` |
| `ObcNotificationMenuItem` | Dropdown menu row | **Props**: `title`, `description`, `day`, `time`, `primaryActionLabel` |
| `ObcNotificationFloatingItem` | Toast overlay | **Slots**: `title`, `description`, `time`, `action` |

```tsx
<ObcNotificationMessageItem title="Engine alert" description="Temp high" time="09:12" type="alarm" />
<ObcNotificationFloatingItem type="warning" hasTimestamp action>
  <span slot="title">Course deviation</span>
  <span slot="description">Off track by 0.3 NM</span>
  <span slot="time">14:32:10</span>
  <span slot="action">Acknowledge</span>
</ObcNotificationFloatingItem>
<ObcNotificationMenuItem title="Update" description="Firmware v2.1" day="Today" time="08:00" />
```

---

## Toggle Switch, Cards & Input Fields

**ObcToggleSwitch** — `label`, `checked`, `disabled`, `hasDescription`, `description`, `hasIcon`, `hasBottomDivider`. Icon via `icon` slot. Event: `input`.

**ObcCard** — flat container. Slots: `title`, default (body). Optional dialog via `hasDialog`.

**ObcElevatedCard** — raised clickable card. Slots: `graphic`, `leading-icon`, `label`, `description`, `status`, `action`, `trailing-icon` (enable with `has*` props). Props: `position`, `size`, `compact`, `border`, `href`. Event: `action-click`.

**ObcTextInputField** — `value`, `placeholder`, `label`, `type`, `disabled`, `readonly`, `error`, `errorText`, `helperText`, `required`, `hasClearButton`, `hasLeadingIcon`. Events: `input`, `change`, `clear`, `blur`.

**ObcNumberInputField** — same as text input, adds `unit` prop for suffix (e.g. "kn").

```tsx
<ObcToggleSwitch label="Auto-pilot" checked hasDescription description="Automatic steering" />
<ObcCard><span slot="title">Voyage</span><div>Content</div></ObcCard>
<ObcElevatedCard hasLeadingIcon hasAction>
  <obi-anchor slot="leading-icon" /><span slot="label">Waypoint</span><span slot="action">Edit</span>
</ObcElevatedCard>
<ObcTextInputField label="Vessel" value={v} placeholder="Search" hasLeadingIcon hasClearButton>
  <obi-search slot="leading-icon" />
</ObcTextInputField>
<ObcNumberInputField label="Speed" value="12.5" unit="kn" />
```

---

## Gotchas

1. **ObcButton label is CHILDREN.** `<ObcButton>Text</ObcButton>` — never `label="Text"`.
2. **ObcAppButton DOES use a `label` prop** — do not confuse with ObcButton.
3. **Notifications are NOT alerts.** Alerts are a separate system (openbridge-alerts skill).
4. **FloatingItem uses slots; MenuItem/MessageItem use props.** Mixing causes blank renders.
5. **ObcTooltip is the tooltip element itself** — it does not wrap a target. Position it manually.
6. **Icon buttons require an icon child** in the default slot or they render empty.