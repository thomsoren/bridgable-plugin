---
name: openbridge-instruments
description: Navigation instruments for OpenBridge maritime applications. Covers compass (ObcCompass, ObcCompassFlat), azimuth thruster (ObcAzimuthThruster), tunnel thruster (ObcThruster), rudder (ObcRudder), watch/gauge instruments (ObcWatch, ObcWatchFlat), and instrument fields (ObcInstrumentField). Use when building instrument dashboards, navigation displays, engine readouts, or any maritime gauges and indicators.
---

# Navigation Instruments

## Critical: Container Sizing

Every circular/radial instrument measures its parent at runtime. No explicit width/height = blank render.

```tsx
{/* CORRECT */} <div style={{ width: 512, height: 512 }}><ObcCompass heading={45} /></div>
{/* WRONG */}   <ObcCompass heading={45} />
```

Minimums: circular instruments 256px, azimuth thruster 512px, flat instruments height 72px.

## Component Reference

| Component | React Import Path | Min Size | Purpose |
|---|---|---|---|
| `ObcCompass` | `navigation-instruments/compass/compass.js` | 256x256 | 3D rotating compass rose |
| `ObcCompassFlat` | `navigation-instruments/compass-flat/compass-flat.js` | 352x72 | Horizontal heading tape |
| `ObcAzimuthThruster` | `navigation-instruments/azimuth-thruster/azimuth-thruster.js` | 512x512 | Thruster angle + thrust |
| `ObcThruster` | `navigation-instruments/thruster/thruster.js` | 512x512 | Tunnel/bow thruster |
| `ObcRudder` | `navigation-instruments/rudder/rudder.js` | 256x256 | Rudder angle indicator |
| `ObcWatch` | `navigation-instruments/watch/watch.js` | 256x256 | Generic circular watch face |
| `ObcWatchFlat` | `navigation-instruments/watch-flat/watch-flat.js` | 352x72 | Horizontal linear gauge |
| `ObcInstrumentField` | `navigation-instruments/instrument-field/instrument-field.js` | auto | Label+value readout |
| `ObcMainEngine` | `navigation-instruments/main-engine/main-engine.js` | 512x512 | Engine RPM/power |
| `ObcInstrumentRadial` | `building-blocks/instrument-radial/instrument-radial.js` | 256x256 | Low-level radial gauge |

All imports prefix: `@oicl/openbridge-webcomponents-react/`

## Compass

`ObcCompass` -- 3D compass rose. `ObcCompassFlat` -- horizontal heading tape.

Compass props: `heading` (0-360), `courseOverGround`, `headingSetpoint`, `windSpeed`, `windFromDirection`, `currentSpeed`, `currentFromDirection`, `direction` (CompassDirection), `showLabels`.
Flat props: `heading`, `courseOverGround`, `FOV` (default 45), `FOVIndicator` (boolean).

```tsx
<div style={{ width: 512, height: 512 }}>
  <ObcCompass heading={127} courseOverGround={130} showLabels windSpeed={12} windFromDirection={45} />
</div>
<div style={{ width: 600, height: 72 }}>
  <ObcCompassFlat heading={270} courseOverGround={268} FOV={90} />
</div>
```

## Azimuth Thruster

Dual-axis: rotation angle + thrust power. Design spec is 512x512. Props: `angle` (rotation deg), `thrust` (0-100%), `angleSetpoint`, `thrustSetpoint`, `singleDirection`, `loading` (0-1), `state` (InstrumentState), `priority` (Priority), `topPropeller`/`bottomPropeller` (PropellerType), `starboardPortIndicator`.

**Gotcha:** Do not flex-center or pad the container. The internal watch measures its parent and renders blank if the box is distorted. Use a plain 512x512 div.

```tsx
<div style={{ width: 512, height: 512 }}>
  <ObcAzimuthThruster angle={35} thrust={72} showLabels />
</div>
```

## Tunnel Thruster (ObcThruster)

Lateral thrust only, no angle dial. Set `tunnel` for tunnel-thruster visuals. Props: `thrust`, `tunnel` (boolean), `singleSided`, `singleDirection`, `setpoint`, `topPropeller`/`bottomPropeller`.

```tsx
<div style={{ width: 512, height: 512 }}>
  <ObcThruster thrust={40} tunnel singleSided />
</div>
```

## Rudder

Semi-circular rudder deflection gauge. Arc sectors via `variant` (180, 120, 90, 60 deg).

Props: `angle` (signed, negative=port), `maxAngle` (default 90), `variant` (ObcRudderVariant), `setpoint`, `showLabels`, `tickmarksInside`.

```tsx
<div style={{ width: 512, height: 512 }}>
  <ObcRudder angle={-15} maxAngle={45} showLabels />
</div>
```

## Watch / Watch Flat

`ObcWatch` -- generic circular face for custom gauges via `needles`, `areas`, `tickmarks` arrays. `ObcWatchFlat` -- horizontal linear variant. Both need sized containers.

Watch props: `needles` (WatchNeedle[]), `areas` (WatchArea[]), `tickmarks` (Tickmark[]), `angleSetpoint`, `clipTop`/`clipBottom`, `rotation`. WatchFlat props: `width`, `height`, `tickmarks`, `labels`, `rotation`, `angleSetpoint`.

## Instrument Field

Auto-sized rectangular readout -- no sized container needed. Props: `tag` (label), `unit`, `value`, `maxDigits`, `fractionDigits`, `showZeroPadding`, `setpoint`, `size` (InstrumentFieldSize), `horizontal`, `off`, `src`, `neutralColor`.

```tsx
<ObcInstrumentField tag="SOG" unit="kn" value={12.4} maxDigits={4} fractionDigits={1} />
<ObcInstrumentField tag="HDG" unit={"\u00B0"} value={127} maxDigits={3} horizontal />
```

## Main Engine

Dual-bar engine readout: thrust + speed (RPM). Props: `thrust` (0-100), `speed`, `thrustSetpoint`, `speedSetpoint`, `state`, `priority`. Needs 512x512 container.

## Dashboard Composition

Use CSS Grid. Pair each circular instrument with an `ObcInstrumentField` readout beneath it.

```tsx
<div style={{ display: "grid", gridTemplateColumns: "repeat(3, 512px)", gap: 24 }}>
  {/* Column: Compass */}
  <div>
    <div style={{ width: 512, height: 512 }}><ObcCompass heading={127} showLabels /></div>
    <ObcInstrumentField tag="HDG" unit={"\u00B0"} value={127} maxDigits={3} />
  </div>
  {/* Column: Azimuth thruster */}
  <div>
    <div style={{ width: 512, height: 512 }}><ObcAzimuthThruster angle={35} thrust={72} /></div>
    <ObcInstrumentField tag="THR" unit="%" value={72} maxDigits={3} />
  </div>
  {/* Column: Rudder */}
  <div>
    <div style={{ width: 512, height: 512 }}><ObcRudder angle={-5} maxAngle={45} /></div>
    <ObcInstrumentField tag="RUD" unit={"\u00B0"} value={-5} maxDigits={3} />
  </div>
</div>
```

Place `ObcCompassFlat` full-width above/below for a heading tape. Use `ObcInstrumentField` rows for text-only panels.
