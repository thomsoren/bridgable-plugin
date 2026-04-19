---
name: openbridge-automation
description: Automation and P&ID components for OpenBridge maritime applications. Covers automation button (ObcAutomationButton with icon slot), tank level indicators (ObcAutomationTank), valves (ObcAnalogValve, ObcDigitalValve), pump (ObcPump), line/pipe elements, and P&ID diagram composition with 24px grid positioning. Use when building automation panels, tank monitoring, valve controls, piping diagrams, or process flow displays.
---

# Automation & P&ID Components

## The 24px Grid System

**Everything in P&ID uses a 24px grid.** All positioning uses `position: absolute` with `calc(G * N)` where `G = 24px`.

```css
.diagram { --g: 24px; position: relative; }
.diagram > * { position: absolute; }
```

### How Components Position on the Grid

**Lines** render their pipe centerline FROM the element's top-left coordinate. Internally, SVGs use negative offsets (`top: -12px`, `left: -12px`) to center the drawn pipe on that coordinate. This means the coordinate you set IS the pipe centerline.

**Corners** occupy a 24×24 box but also use `-12px` SVG offsets. They sit at INTEGER grid positions.

**Devices** (pump, valve, tank) use `positioning="point"` which renders the component centered on the coordinate via `translateX(-50%)` / `translateY(-50%)` transforms. The coordinate IS the pipe connection point.

### Critical Connection Rules

```
Lines start at corner + 0.5 grid units in the extend direction.
Lines end at next corner - 0.5 grid units.
Lines pass THROUGH devices (devices sit on top via z-index).
```

**Example: corner at (10, 16), next corner at (22, 16):**
- Line starts at left = `10.5 * G`, length = `11` (ends at 21.5, leaving 0.5 gap to corner at 22)

**Example: device at (18, 16) between two line segments:**
- Line A: starts at `14.5`, length `3.5` (passes through device center at 18)
- Line B: starts at `18.5`, length `3` (continues from device, ends before next corner)
- Device z-index: 2, lines z-index: 1 — device visually covers the line underneath

### Z-Index Layering

```css
obc-horizontal-line, obc-vertical-line, obc-corner-line, obc-three-way-line { z-index: 1; }
obc-pump, obc-analog-valve, obc-digital-valve, obc-automation-button { z-index: 2; }
obc-automation-tank { z-index: 3; }
```

---

## Components

All imports from `@oicl/openbridge-webcomponents-react`:

| Component | Import Subpath | Purpose |
|-----------|---------------|---------|
| `ObcAutomationTank` | `automation/automation-tank/automation-tank.js` | Tank level indicator |
| `ObcPump` | `automation/pump/pump.js` | Pump — generates its own icon |
| `ObcAnalogValve` | `automation/analog-valve/analog-valve.js` | Continuous valve (0-100%) |
| `ObcDigitalValve` | `automation/digital-valve/digital-valve.js` | On/off valve |
| `ObcAutomationButton` | `automation/automation-button/automation-button.js` | Generic button — REQUIRES icon slot |
| `ObcHorizontalLine` | `automation/horizontal-line/horizontal-line.js` | Horizontal pipe segment |
| `ObcVerticalLine` | `automation/vertical-line/vertical-line.js` | Vertical pipe segment |
| `ObcCornerLine` | `automation/corner-line/corner-line.js` | 90° pipe turn |
| `ObcThreeWayLine` | `automation/three-way-line/three-way-line.js` | T-junction |
| `ObcEndPointLine` | `automation/end-point-line/end-point-line.js` | Pipe terminator |
| `ObcMotor` | `automation/motor/motor.js` | Motor device |
| `ObcFan` | `automation/fan/fan.js` | Fan device |

---

## Tank (`ObcAutomationTank`)

**Positions from center-bottom.** The CSS applies `translateX(-50%)` and `top: -20px`, so the coordinate you set is where the pipe connects at the tank's bottom center.

Props: `value` (absolute number), `max` (capacity), `medium` (LineMedium — sets fill color), `trend` (TankTrend integer: -2 fastFalling, -1 falling, 0 stable, 1 rising, 2 fastRising), `variant` (`"compact"` for small), `tag` (label like `"T-001"`).

Level is `value / max`, NOT a percentage. `value={3000} max={5000}` shows 60%.

## Pump (`ObcPump`)

**Use this instead of `ObcAutomationButton` for pumps.** It auto-generates the correct pump icon based on `on` and `vertical` props — no manual icon slot needed.

Props: `on` (boolean), `vertical` (boolean), `positioning` (`"point"` for P&ID), `tag` (string).

## Valves (`ObcAnalogValve`, `ObcDigitalValve`)

**Use these instead of valve icon components.** They are full components with state, readout, and alert support.

| Component | Use Case | Key Props |
|-----------|----------|-----------|
| `ObcAnalogValve` | Continuous (0-100%) | `open`, `value` (0-100), `vertical`, `positioning` |
| `ObcDigitalValve` | Binary on/off | `open`, `vertical`, `positioning` |

Both inherit `positioning`, `tag`, `readoutPosition`, `alert` from `ObcAbstractAutomationButton`.

**Set `positioning="point"` for P&ID diagrams.** This centers the device on the grid coordinate.

## Lines

All share: `medium` (`"normal"` | `"empty"` | `"water"` | `"oil"` | `"air"`), `lineType` (`"fluid"` | `"electric"` | `"air"` | `"connector"`).

| Component | Key Prop | Notes |
|-----------|----------|-------|
| `ObcHorizontalLine` | `length` (grid units) | Width = length × 24px |
| `ObcVerticalLine` | `length` (grid units) | Height = length × 24px |
| `ObcCornerLine` | `direction` (top-left, top-right, bottom-left, bottom-right) | 24×24 box |
| `ObcThreeWayLine` | `direction` (top, right, bottom, left) — branch direction | 24×24 box |

**Lengths can be fractional:** `length="3.5"` = 84px. Use `.5` values for precise corner connections.

---

## Medium Flow Logic

When a valve is closed, split the pipeline into BEFORE and AFTER segments:
- **Before valve:** shows pump medium (oil/water if pump is running, empty if not)
- **After valve:** shows empty (no flow past closed valve)

Give each line segment its own ID so you can set medium per-segment.

---

## Complete P&ID Example

```
Tank1(5,6) → down → corner(5,16) → right → Pump(8,16) → right → Junction(14,16)
  Junction → right → Valve1(18,16) → right → corner(22,16) → down → Tank2(22,24)
  Junction → up → Valve2(14,12) → up → corner(14,8) → right → corner(32,8) → down → Tank3(32,24)
```

```html
<div class="diagram" style="--g: 24px; position: relative;">
  <!-- Tank 1 — output at grid (5, 6) -->
  <obc-automation-tank id="tank1" tag="T-001" max="5000" value="3000"
    style="position:absolute; top: calc(var(--g)*6); left: calc(var(--g)*5);"></obc-automation-tank>

  <!-- Down from tank to corner -->
  <obc-vertical-line length="2.5" medium="oil" lineType="fluid"
    style="position:absolute; top: calc(var(--g)*13); left: calc(var(--g)*5);"></obc-vertical-line>

  <!-- Corner: turn right -->
  <obc-corner-line direction="top-right" medium="oil" lineType="fluid"
    style="position:absolute; top: calc(var(--g)*16); left: calc(var(--g)*5);"></obc-corner-line>

  <!-- Right to pump -->
  <obc-horizontal-line length="3" medium="oil" lineType="fluid"
    style="position:absolute; top: calc(var(--g)*16); left: calc(var(--g)*5.5);"></obc-horizontal-line>

  <!-- Pump at (8, 16) -->
  <obc-pump tag="P-001" on positioning="point"
    style="position:absolute; top: calc(var(--g)*16); left: calc(var(--g)*8); z-index:2;"></obc-pump>
</div>
```

---

## Gotchas

1. **Use `ObcPump` for pumps, `ObcAnalogValve`/`ObcDigitalValve` for valves** — NOT `ObcAutomationButton` with manual icons. The dedicated components generate correct icons automatically.
2. **`positioning="point"` is required** for P&ID devices. Without it, the component's bounding box positions from top-left instead of center, breaking all alignment.
3. **Corners at integers, lines at ±0.5.** Lines that overlap into a corner's grid cell cause visible artifacts (double borders).
4. **Lines pass through devices.** Don't leave a gap — make the line long enough to span through the device's position. The device covers the line via z-index.
5. **Tank positions from center-bottom.** Its `top` coordinate is the pipe connection point, NOT the visual top of the tank.
6. **Valve folder typo:** `valve-analoge-two-way-icon` (extra "e" in folder). Import path must match.
7. **`horisontal` prop typo:** Valve icon components use `horisontal` not `horizontal`.
8. **Split medium per-segment** for accurate flow visualization when valves are closed.
