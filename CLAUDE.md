# CLAUDE.md — MMITS Hiraya Transit v16
> Project brief for Claude Code. Read this entire file before writing a single line of code.

---

## 0. Mission

Rebuild the Philippine transit app **MMITS (Metro Manila Integrated Traffic Safety)** from v15 into a single, self-contained `index.html` file designated **v16**. This is simultaneously a full visual overhaul (Liquid Glass / Apple aesthetic) and a functional upgrade (static SVG map → interactive Leaflet.js NCR map). Output **one file only** — no external JS files, no build step, no framework, pure vanilla HTML / CSS / JS.

The v15 source file (`index.html`) is provided alongside this document. Treat it as the functional reference. All existing logic must be preserved; only the rendering layer and map engine are replaced.

---

## 1. Identity

| Token | Value |
|---|---|
| App name (primary) | **MMITS** |
| Sub-brand (below primary) | **Hiraya Transit** — 10 px / weight 400 / muted color, directly under "MMITS" |
| Tagline | **Bagong Galaw** |

Both the floating header wordmark **and** the onboarding splash must display MMITS on line 1 and "Hiraya Transit" on line 2. The tagline "Bagong Galaw" appears on the onboarding screen only.

---

## 2. Typography

- **Font family:** `Plus Jakarta Sans` — import from Google Fonts, weights 300 / 400 / 500 / 700.
- **font-display: swap** on the `@import` so the UI renders immediately if the font is slow.
- Apply to **all** text — headings, labels, nav items, onboarding copy, map labels. Replace the v15 `Syne` / `DM Sans` stack entirely.

| Role | Size | Weight | Notes |
|---|---|---|---|
| Hero | 32 px | 700 | Onboarding title |
| Title | 18 px | 600 | Screen headings |
| Body | 13 px | 400 | General copy |
| Caption | 10 px | 500 | Uppercase, `letter-spacing: 0.08em` |

---

## 3. Aesthetic — Liquid Glass + Apple

### 3.1 Glass Recipe

All **floating surfaces** — header, bottom nav, search bar, mode pill, bottom sheet, right control stack — must use this exact recipe:

```css
/* Dark mode (default) */
backdrop-filter: blur(28px) saturate(1.9);
background: rgba(255, 255, 255, 0.08);
border: 1px solid rgba(255, 255, 255, 0.18);

/* Light mode (html.light) */
background: rgba(255, 255, 255, 0.72);
```

Cards and sheets use the same recipe with slightly higher opacity (`0.12` dark / `0.82` light).  
**No hard flat colors on any surface background** — everything is translucent over the map or content below.

### 3.2 Accent Palette

| Token | Hex | Usage |
|---|---|---|
| Gold | `#f5a623` | Primary action, CTAs, active states |
| Teal | `#3ecfcf` | Live badges, transit indicators |
| Purple | `#7c5cbf` | Secondary accents |
| Red | `#ff4f4f` | SOS **only** |
| Green | `#3ecf8e` | Traffic flowing, LRT-1 line |

### 3.3 Corner Radii

| Element | Radius |
|---|---|
| Buttons | 14 px |
| Cards | 22 px |
| Pills / tags | 999 px |
| Modals / sheets | 28 px |

### 3.4 Onboarding Glow

Place a large radial gradient glow behind the MMITS wordmark: gold, 40 % opacity, ~300 px radius, CSS `blur` filter. This is purely decorative and must not affect layout.

### 3.5 Bottom Nav Active States

Active nav items use a **pill-shaped glass fill** (not just an icon color change). The pill background uses the glass recipe at ~0.15 opacity with a gold tint.

---

## 4. Transitions & Motion

| Trigger | Effect | Duration | Easing |
|---|---|---|---|
| Screen switch | `opacity 0→1` + `translateY(12px)→0` | 320 ms | `cubic-bezier(0.25, 0.46, 0.45, 0.94)` |
| Bottom sheet open / close | `translateY(100%)→0` | 400 ms | `cubic-bezier(0.32, 0.72, 0, 1)` |
| Button press | `scale(0.94)` on `:active` | 120 ms | `ease-out` |
| Nav item tap | `::after` ripple, opacity fade | 200 ms | — |
| Mode pill segment | Background slides | 280 ms | `cubic-bezier(0.22, 1, 0.36, 1)` |

---

## 5. Screens

All five screens from v15 must be rebuilt. Screen switching uses only `classList.add / remove` — **never `innerHTML` replacement**.

### 5.1 Onboarding
- Full-screen splash with gold glow behind the MMITS + Hiraya Transit wordmark.
- Persona select pills (commuter / driver / cyclist — preserve v15 options).
- Name input field.
- CTA button (gold, 14 px radius).
- "Bagong Galaw" tagline visible on this screen.

### 5.2 Map
- Leaflet.js interactive map (see §7) fills the full screen behind all glass overlays.
- **Floating glass header** — left side: MMITS wordmark + "Hiraya Transit" sub-brand; right side: live badge + eco toggle.
- **Glass search bar** below the header for destination input / routing trigger.
- **Glass mode pill** (Drive / Transit / Walk) — three segments, sliding active background.
- **Right control stack** — zoom in, zoom out, locate (snap-to-GPS), layers, SOS.
- **Bottom sheet** slides up on map marker tap showing pin detail.
- **Aurora background** — see §5.2.1.

#### 5.2.1 Aurora Background
A subtle animated layer sits between the Leaflet map and the glass UI overlays:
- Three slow-moving radial gradients (gold, teal, purple) using a CSS `@keyframes` loop.
- Animation duration: 8 s, looping, `pointer-events: none`.
- Opacity of the entire aurora layer: **~0.06** — barely perceptible, purely atmospheric.
- **Graceful degradation:** If CSS animations are not supported, the layer simply stays at its start-position static gradient. No JS fallback needed.

### 5.3 Report Incident
- Glass card form.
- Category chips (Accident, Flood, Road Work, Stalled Vehicle — preserve v15 list).
- Photo placeholder with upload icon.
- Submit button (gold).

### 5.4 Transit Tracker
- LRT-1, LRT-2, MRT-3 card list.
- Live animated position bars per v15 logic.
- **Thin animated indicator line** under the active / selected train card: left-to-right sweep, 4 s loop, teal color.
- Tapping a train card triggers **Transit Follow Mode** — the Leaflet map view pans to the train's simulated position (replaces the v15 SVG viewBox zoom).

### 5.5 Profile
- User card with name from onboarding.
- Settings toggles: Notifications, Eco Mode, Dark/Light theme switcher.
- Eco Mode toggle updates a global state used by the header badge.

---

## 6. Layout Personality Details

### Floating Header
```
[ MMITS            (bold, 18 px)  ]   [ LIVE badge ][ ECO toggle ]
[ Hiraya Transit   (muted, 10 px) ]
```
- Left-aligned wordmark, no center alignment.
- Right side shows: a teal "● LIVE" pill + the eco mode toggle (gold leaf icon, toggles on/off).

### SOS Button
- Located in the right control stack on the Map screen.
- **Pulses with a soft red glow every 3 seconds** using a CSS `@keyframes` animation with a long pause (`animation: sos-pulse 3s ease-in-out infinite`).
- Not a constant glow — one pulse every 3 s so it feels purposeful, not alarming.
- Tapping opens the existing SOS modal from v15.

### Bottom Nav Bar
- No `border-top` — the blur line at the top of the nav is created solely by `backdrop-filter`.
- Glass background using the standard recipe.

---

## 7. NCR Map Engine (Leaflet.js — v16 Core Addition)

### 7.1 Loading
Load Leaflet from CDN inside the `<head>`:
```html
<link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
```
Fallback CDN if unpkg is unavailable: `https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.9.4/`.

### 7.2 Base Tile
```
https://{s}.basemaps.cartocdn.com/dark_all/{z}/{x}/{y}{r}.png
```
Attribution: `© OpenStreetMap contributors © CARTO`

### 7.3 NCR Bounds & Zoom
```js
const NCR_BOUNDS = L.latLngBounds(
  L.latLng(14.35, 120.90),  // SW corner
  L.latLng(14.80, 121.15)   // NE corner
);
const map = L.map('map-container', {
  center: [14.5995, 120.9842],
  zoom: 12,
  minZoom: 11,
  maxZoom: 17,
  maxBounds: NCR_BOUNDS,
  maxBoundsViscosity: 1.0,
  zoomControl: false  // custom controls used instead
});
```

### 7.4 Stoplight Markers
- Place custom pulsing markers at 10–15 major NCR intersections (EDSA-Taft, EDSA-Ortigas, EDSA-Ayala, España-Lacson, C5-BGC, etc.).
- Each marker cycles: **Green (30 s) → Yellow (10 s) → Red (30 s)** using `setInterval`.
- Use `L.divIcon` with a CSS-animated circle for the pulsing effect.
- The interval must be stored and cleared when leaving the Map screen.

### 7.5 GPS & Location Dot
- Call `navigator.geolocation.watchPosition()` on Map screen activation.
- Place a "User Pulse" marker: a teal dot with an expanding ring CSS animation.
- If geolocation is denied or unavailable, silently fall back to NCR center `[14.5995, 120.9842]` with a static dot. No error alert to the user.
- The **locate button** in the right control stack calls `map.flyTo(userLatLng, 15)`.

### 7.6 Traffic-Aware Routing
- **Trigger:** User types a destination in the search bar and presses Enter, or long-presses anywhere on the map.
- **Destination resolution:** Use a hardcoded lookup table of ~20 common NCR destinations (SM Mall of Asia, BGC, Intramuros, Trinoma, Araneta, etc.) mapped to lat/lng. If the typed string matches, route to that coordinate. If no match, pick a random point 1–3 km from the user in a valid NCR direction.
- **Route simulation:** Generate a multi-segment polyline between user position and destination with 4–8 intermediate waypoints.
- **Traffic coloring:** Assign each segment a random traffic state on generation. Render with:
  - Green `#3ecf8e` — flowing, weight multiplier 1×
  - Yellow `#f5a623` — slow, weight multiplier 1.6×
  - Red `#ff4f4f` — congested, weight multiplier 2.8×
- **Routing logic:** When multiple possible path orderings exist, always prefer the one with the lowest total weighted time even if total distance is longer. This is simulated by generating 3 candidate routes and picking the lowest-weight-time one.
- **Display:** Show an info chip at the top of the bottom sheet with "ETA ~X min · X.X km".
- Clear the route polyline layers when the bottom sheet is closed or the mode pill changes.

### 7.7 Transit Follow Mode (Updated)
When a train card is tapped in Transit Tracker:
1. Switch to the Map screen.
2. Call `map.flyTo([trainLat, trainLng], 14, { animate: true, duration: 1.2 })`.
3. Show a "Following [Line Name]" glass badge on the map that dismisses after 4 s.
4. The train's simulated lat/lng is derived from its progress along a hardcoded geographic waypoint array for each line (LRT-1 runs Baclaran → Roosevelt, LRT-2 runs Recto → Antipolo, MRT-3 runs Taft → North Edsa).

### 7.8 Map Initialization Safety
```js
try {
  initLeafletMap();
} catch (e) {
  // Show fallback card
  const fallback = document.getElementById('map-fallback');
  if (fallback) fallback.classList.remove('hidden');
}
```
The fallback is a glass card centered on the map screen reading "Map temporarily unavailable" with a retry button that calls `initLeafletMap()` again.

---

## 8. Anti-Crash Requirements (Mandatory — Zero Exceptions)

These are hard requirements. Every single point must be implemented.

1. **Single DOMContentLoaded wrapper** — all JS lives inside one `document.addEventListener('DOMContentLoaded', () => { ... })`.

2. **Null-check every DOM query:**
   ```js
   const el = document.getElementById('my-element');
   if (!el) return;
   ```
   No bare `.getElementById()` calls without a guard.

3. **CSS transitions only** — no `requestAnimationFrame` loops except:
   - The aurora background effect (CSS `@keyframes` preferred; if rAF is used, wrap in `try/catch`).
   - The v15 train animation loop (preserve as-is but wrap in `try/catch`).

4. **No runtime network calls** — no `fetch()`, no `XMLHttpRequest`, no `dynamic import()`. Leaflet loads via `<script>` tag at parse time only.

5. **Optional chaining on all event listeners:**
   ```js
   element?.addEventListener('click', handler);
   ```

6. **Screen switching via classList only:**
   ```js
   screen.classList.add('active');
   screen.classList.remove('active');
   // NEVER: screen.innerHTML = '...'
   ```

7. **Interval / timeout hygiene:**
   ```js
   let stoplightInterval = null;
   // On screen enter:
   stoplightInterval = setInterval(cycleStoplights, 30000);
   // On screen leave:
   clearInterval(stoplightInterval);
   stoplightInterval = null;
   ```
   This applies to: stoplight cycle, train follow-mode pan, aurora rAF (if used), toast timers, ETA update timers.

8. **Leaflet initialization wrapped in try/catch** (see §7.8).

9. **Marker click handlers check bottom sheet existence:**
   ```js
   marker.on('click', () => {
     const sheet = document.getElementById('bottom-sheet');
     if (!sheet) return;
     sheet.classList.add('open');
   });
   ```

10. **Stoplight and follow-mode intervals** must be cleared before being re-registered on every Map screen activation — prevents stacking if the user navigates away and back.

11. **font-display: swap** on the Google Fonts `@import` / `<link>`.

12. **Aurora graceful degradation** — the aurora layer is a CSS `@keyframes` animation on a `div`. If the browser ignores `@keyframes`, the div simply renders at its initial gradient state. No JS check needed.

13. **Geolocation errors** are caught silently:
    ```js
    navigator.geolocation?.getCurrentPosition(
      (pos) => placeUserDot(pos),
      () => placeUserDot(NCR_CENTER_FALLBACK)
    );
    ```

---

## 9. Preserved v15 Functionality Checklist

The following must work identically to v15 (logic unchanged, visual updated):

- [ ] Onboarding flow: persona select → name input → enter app
- [ ] Screen routing: Map / Report / Transit / Profile via bottom nav
- [ ] Map mode pill: Drive / Transit / Walk — updates active state
- [ ] Theme toggle: Dark ↔ Light — applies `html.light` class, all glass surfaces update
- [ ] Eco mode toggle: header badge updates, stored in app state
- [ ] SOS modal: opens on SOS button tap, has cancel + confirm actions
- [ ] Toast notifications: appear on relevant actions, auto-dismiss
- [ ] Report form: category chip selection, submit clears form + shows toast
- [ ] Transit Tracker: LRT-1 / LRT-2 / MRT-3 cards, animated position bars, ETA display
- [ ] Profile toggles: notifications, eco mode, theme
- [ ] Dynamic Island clock display
- [ ] Status bar elements

---

## 10. File Output Specification

| Property | Value |
|---|---|
| Output filename | `index.html` |
| External dependencies | Leaflet CSS + JS via CDN `<script>`/`<link>` tags in `<head>` only |
| Google Fonts | `<link>` tag in `<head>` with `font-display: swap` |
| Everything else | Inline — `<style>` block + `<script>` block |
| Frameworks | None |
| Module bundlers | None |
| TypeScript | No |
| Min file size target | N/A — correctness over brevity |

---

## 11. Reference: v15 Source Overview

The v15 `index.html` (1 811 lines) contains:

- **CSS variables** for dark/light theming (`--gold`, `--teal`, `--purple`, etc.) — extend these, do not discard.
- **Phone shell** at 360 × 760 px with Dynamic Island and status bar.
- **Five screen `<div>`s** — `#screen-onboarding`, `#screen-map`, `#screen-report`, `#screen-transit`, `#screen-profile`.
- **Static SVG map** — replace entirely with the Leaflet `<div id="map-container">`.
- **`LINE_CONFIGS` object** with waypoints for LRT-1, LRT-2, MRT-3 — preserve and extend with geographic lat/lng for follow mode.
- **`runTrainAnimation()`** rAF loop — keep, but add geographic coordinate interpolation for Leaflet follow mode.
- **`showToast()`**, **`switchScreen()`**, **`setTheme()`** helper functions — preserve all.
- **`HALF` time constant** and train position math — preserve exactly.

---

## 12. Quick Reference: Key Element IDs to Preserve

```
#screen-onboarding   #screen-map       #screen-report
#screen-transit      #screen-profile
#bottom-sheet        #bottom-nav       #map-container (NEW — replaces SVG)
#map-fallback        (NEW — fallback card)
#sos-modal           #toast
#search-bar          #mode-pill
#eco-toggle          #theme-btn
#linearRouteLRT1     #linearRouteLRT2  #linearRouteMRT3
```

All v15 IDs must be retained. New v16 IDs are additive.

---

## 13. In-Progress Plan (context recovery — last updated 2026-04-30)

Three upgrades were approved and are in implementation:

### A. Agent Potato — OSM Track & Road Geometry
- **Goal**: Fetch real polyline coords from Overpass API for rail lines (LRT-1/2, MRT-3) and major NCR highways.
- **Output files**: `agent-potato-data/road_geometries.json`, `agent-potato-data/rail_geometries.json`
- **Status**: Agent Potato spawned; awaiting data files. Once files exist, hardcode into HTML as `ROAD_CONGESTION_DATA` and optionally extend `GEO_LRT1/2/MRT3` with intermediate waypoints from `rail_geometries.json`.
- **OSM relation IDs**: LRT-1 = 5870086, LRT-2 = 5870085, MRT-3 = 1870350.

### B. Stoplight Icon — SVG Traffic Light ✅ DONE
- Replaced the old 14×14 CSS circle (`.sl-marker`) with an SVG vertical traffic light icon.
- New CSS classes: `.sl-icon-wrap`, `.sl-dot-red/yellow/green`, `.sl-state-green/yellow/red`.
- `L.divIcon` now embeds a 20×32px SVG housing with 3 colored circles; active dot lights up via parent class.
- `iconSize:[20,32]`, `iconAnchor:[10,32]` (bottom-center of icon).
- Functions affected: `startStoplights()` lines ~2018–2029; CSS lines ~137–144.

### C. Road Congestion Overlay — PENDING Agent Potato data
- **Goal**: Draw persistent colored polylines over major NCR roads, 4-color coded (green/yellow/orange/red), updated every 90 seconds.
- **New constant**: `ROAD_CONGESTION_DATA` — array of 17 road objects with `name`, `roadClass`, `speedKph`, `coords[]`. Coords come from `road_geometries.json`.
- **New functions**: `drawRoadCongestion()`, `clearRoadCongestion()`, `startCongestionUpdates()`, `stopCongestionUpdates()`.
- **New variables**: `roadCongestionLayers=[]`, `congestionInterval=null` (add after line ~1748).
- **Custom pane**: `leafletMap.createPane('congestion')` with `zIndex: 200` (below default overlay pane 400).
- **4-state TRAFFIC_STATES**: Green >40 kph, Yellow 25–40, Orange 15–25, Red ≤15. Update `speedToTrafficState()` thresholds.
- **Zoom guard**: `if(leafletMap.getZoom()<12)return` in `drawRoadCongestion()`. Add `zoomend` listener.
- **Integration**: Call `startCongestionUpdates()` in `initLeafletMap()` after `startStoplights()`. Call in `switchTab()` map branch; call `stopCongestionUpdates()` in non-map branch.

### Order of operations (remaining)
1. Wait for Agent Potato to commit `road_geometries.json` and `rail_geometries.json`.
2. Build `ROAD_CONGESTION_DATA` from `road_geometries.json` — copy names/speeds from `TRAFFIC_OPEN_FEED_SAMPLE`, add `coords[]`.
3. Add 4-color `TRAFFIC_STATES` + updated `speedToTrafficState()`.
4. Add four congestion functions + pane creation + integration into `initLeafletMap()` and `switchTab()`.
5. (Optional) Update `GEO_LRT1/2/MRT3` with rail waypoints from `rail_geometries.json`.
6. Commit and push.

---

*End of CLAUDE.md — MMITS Hiraya Transit v16*
