# CLAUDE.md — MMITS Hiraya Transit v16
> Project brief for Claude Code. Read this entire file before writing a single line of code.

---

## RESPONSE DEFAULTS (apply to every reply unless overridden)

- Answer directly. No preamble, filler, affirmations, or trailing summary clauses.
- Use plain prose or tight lists. No decorative headers for short answers.
- Do not use Extended Thinking or web search unless the prompt is explicitly complex or time-sensitive.
- If a task is simple (formatting, grammar, short translation), note once that Haiku may suffice.
- At 15+ messages, offer once to summarize key context for a fresh chat.
- If a correction is requested, note once that editing the last message saves tokens.

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

## 13. Completed Features (last updated 2026-04-30) — ALL DONE ✅

### A. Stoplight Icon — SVG Traffic Light ✅
- 14×14px CSS circles replaced with 20×32px SVG vertical traffic lights (dark housing, 3 colored dots).
- CSS: `.sl-icon-wrap`, `.sl-dot-red/yellow/green`, `.sl-state-green/yellow/red`.
- Phase cycling updates parent class (`sl-state-*`); CSS child selectors light the correct dot.

### B. Road Congestion Overlay ✅
- `ROAD_CONGESTION_DATA`: 17 NCR road segments with OSM Overpass-derived polyline coords (15–34 pts each vs old 5-pt placeholders).
- Real EDSA geometry via WebFetch to Overpass API (52 points, proper northeast curve through Guadalupe).
- 4 traffic states: green >40, yellow 25–40, orange 15–25, red ≤15 kph.
- Functions: `drawRoadCongestion()`, `clearRoadCongestion()`, `startCongestionUpdates()`, `stopCongestionUpdates()`.
- Zoom guard (hide at zoom < 12), 90s drift update, congestion Leaflet pane at zIndex 200.

### C. Rail Track Waypoints ✅
- `TRACK_LRT1` (39 pts), `TRACK_LRT2` (25 pts), `TRACK_MRT3` (20 pts) added from `rail_geometries.json`.
- `TRACK_COORDS = {lrt1, lrt2, mrt3}` object for lookup.
- `interpolatePolyline(pts, t)` helper: finds exact `[lat,lng]` at fractional position along a polyline.
- `getTrainGeoPos()` now calls `interpolatePolyline(TRACK_COORDS[line], (i+t)/(n-1))` instead of straight-line lerp.

### D. Mode-Based Layer Visibility ✅
- **Driver mode** (default): road congestion + stoplights ON; train dots HIDDEN.
- **Commuter mode**: transit lines + train dots ON; road congestion + stoplights OFF.
- **Custom mode**: everything ON.
- `showTrainDots()` / `hideTrainDots()` — add/remove all `trainDotMarkers` from Leaflet map.
- `selectMode()` calls the appropriate show/hide functions per mode branch.
- On initial load: `if(currentMode==='driver')hideTrainDots()` called after `initTrainMarkers()`.

### Key files modified
- `MMITS Hiraya Transit.html` — all changes inline
- `agent-potato-data/road_geometries.json` — 5-pt placeholders (superceded by inline ROAD_CONGESTION_DATA)
- `agent-potato-data/rail_geometries.json` — track waypoints (integrated into TRACK_* arrays)

---

---

## 14. New Features — v16.1 (2026-05-01)

### E. Data Privacy Consent Checkbox ✅ (planned)
- Add a checkbox row at the bottom of the onboarding form, above the Start button.
- Text: "I agree that my personal data is collected and processed under the Data Privacy Act of 2012 (Republic Act No. 10173). <a>Terms & Conditions</a>"
- Clicking "Terms & Conditions" opens a lightweight in-app modal (not a new tab) with a short summary.
- The "Start My Journey" button stays disabled until: (a) role is selected AND (b) checkbox is checked.
- Skipping bypasses the checkbox (anonymous mode — see §14.F).
- CSS class: `.ob-privacy-row`, `.ob-privacy-link`, `.terms-modal`

### F. Anonymous / Guest Mode ✅ (planned)
- Add `let isGuest = false;` global state.
- When `startJourney(true)` (skip), set `isGuest = true` and do NOT save any profile data.
- `switchTab('profile')` checks `isGuest`:
  - **Guest**: Show an anonymous avatar (`?`), username "Guest User", a message "Register or login to see your profile and points", and two gold-styled buttons: `[Login]` `[Register]` (both trigger `showToast` for now).
  - **Logged-in**: Show the normal full profile (current behavior).
- Profile screen HTML must contain both views; toggle visibility via `classList.add/remove('hidden')`.
- IDs: `#profile-guest-view`, `#profile-user-view`

### G. Corrected Train Track Coordinates ✅ (planned)
Based on the reference map (manila-metro-map.png, UrbanRail.Net):

**GEO_LRT1 corrections (south terminus)**:
- EDSA: lat 14.5376, lng 120.9924 (was 14.5381, 120.9940)
- Baclaran: lat 14.5197, lng 120.9998 (was 14.5342, 120.9974 — off by ~2km)
- Add Redemptorist-Aseana terminus: lat 14.5155, lng 120.9929

**TRACK_LRT1 corrections**:
- Extend southern end to match corrected Baclaran / Redemptorist coords
- Keeps the same northern FPJ endpoint

**GEO_LRT2 corrections**:
- Santolan: lat 14.6493, lng 121.0844 (was 14.6217, 121.0863 — off by ~3km)
- Marikina: lat 14.6357, lng 121.0975 (was 14.6205, 121.1006)
- Antipolo: lat 14.6249, lng 121.1746 (was 14.6248, 121.1214 — off by ~6km)

**TRACK_LRT2 full replacement**:
- Remove the incorrect big jump from lng 121.0453 to 121.0510
- Reroute to follow GEO_LRT2 stations continuously
- Extend to actual Antipolo endpoint

**GEO_MRT3**: No structural changes needed; minor drift corrections acceptable.

### H. Additional Stoplights ✅ (planned)
Expand `STOPLIGHT_DATASET.items` from 25 to 50+ intersections.
New intersections to add (all on real NCR roads):
- Taft Ave at key crossings (Padre Faura, Vito Cruz, Pablo Ocampo, Libertad)
- Katipunan Ave intersections (Ateneo, UP, Miriam, Blue Ridge)
- Aurora Blvd intersections (Cubao, Gilmore, Katipunan jct)
- Ortigas Ave (Lores, Valle Verde, C5)
- Marcos Highway (Cainta, Marikina-Infanta Hwy)
- Manila streets (Espana-Lacson, Legarda, Recto-Quezon Blvd)
- Makati CBD (Ayala-Paseo, Makati Ave-Buendia, Jupiter-Rockwell)
- Parañaque / Las Piñas (Ninoy Aquino Ave, Alabang-Zapote Rd)
- Marikina / Pasig (Marcos Hwy, Rosario)
All new items must align to actual road intersections (not random field coordinates).

### I. Road Congestion Toggle in Commuter Mode ✅ (planned)
- Add a "Road Colors" toggle row in the `#layerMenu`.
- `let congestionToggleOn = true;` state.
- Toggle calls `clearRoadCongestion()` / `drawRoadCongestion()` accordingly.
- In **commuter mode**: toggle is available and defaults to OFF (roads hidden, transit lines shown).
- In **driver mode**: toggle is always ON (roads shown), toggle hidden/disabled.
- In **custom mode**: toggle available, defaults to ON.
- Layer menu row: icon "🛣️", label "Road Colors", lm-toggle behavior.

### J. Timestamps in Bottom Sheet ✅ (planned)
Move `#mapUpdateStamp` out of the absolute map overlay into the bottom sheet's peek row.
- Remove current CSS: `#mapUpdateStamp{position:absolute;left:12px;bottom:142px;}`.
- Place `#mapUpdateStamp` as the last element inside `.sheet-peek-row` (between the expand hint and right edge).
- When sheet is `state-collapsed` (only handle bar showing): stamp is hidden under the sheet → no obstruction.
- When sheet is `state-peek` or `state-expanded`: stamp is visible within the sheet.
- Apply `font-size:8px; color:var(--muted);` inline or via existing `.update-stamp` class.
- `transitUpdateStamp` stays on the Transit screen (no change).

---

---

## 15. Improvements Applied — v16.2 (2026-05-01)

### E. Notification Map Bubbles ✅
- `placeNotifMapMarkers()` — Leaflet markers for liveNotifs with lat/lng
- `updateNotifMapMarkerVisibility()` — zoom >= 13 + viewport bounds guard
- Called on map init and `zoomend moveend`

### F. Bottom Sheet Peek Cleanup ✅
- Removed "↑ Expand" hint + divider from `.sheet-peek-row`
- Narrowed `.peek-mode-pill` padding to `3px 8px`

### G. SOS Banner Repositioned ✅
- `.loc-share-banner` moved from `top:58px` to `bottom:148px` (no longer blocks search bar)

### H. Train Snap-to-Station ✅
- `getTrainGeoPosSync` returns exact GEO[stationIdx] lat/lng when dwelling
- Interpolates between GEO station coords when traveling (not track interpolation)

### I. Station + Track Coordinates Updated ✅
- GEO_LRT1/LRT2/MRT3 and TRACK_LRT1/LRT2/MRT3 replaced with OSM-accurate data
- GEO_MRT3 corrected to 13 entries matching SVG_MRT3_WP (removed extra Betty Go / Santolan-Anapolis)
- GEO_LRT2 Santolan/Antipolo corrected; TRACK_LRT2 extended continuously to 121.1746

### J. Stoplights Expanded (50+) ✅
- Added Katipunan, Aurora Blvd, Ortigas, Marcos Hwy, Makati CBD, Parañaque, Manila intersections

### K. Notification Resolution Timers ✅
- `scheduleNotifResolutions()` — auto-resolves accident/congestion notifs at 35s / 130s
- Updates map bubble icon to ✅ on resolution

### L. Profile 3-Subtab Layout ✅
- Tabs: 🏆 Leaders | 🎫 Rewards | ⚙️ Settings
- `switchProfileTab(tab)` — toggles `.psc-*` content divs

### M. Community Forecast City Shuffle ✅
- 17 Metro Manila cities with peak-hour data
- Auto-shuffles every 5s; user tap pauses shuffle via `selectForecastCity()`
- Pill row scrollable horizontally

### N. Guest/User Profile Toggle ✅
- `doLogin()` / `doRegister()` call `applyUserProfile()` which sets `isGuest=false` and shows `#profile-user-view`
- `switchTab('profile')` re-evaluates `isGuest` on every visit

---

## 16. Improvements Applied — v16.3 (2026-05-01)

### A. LRT-1 South Extension ✅
- 5 new stations added: Redemptorist, MIA Road, PITX, Ninoy Aquino Ave, Dr. Santos
- SVG_LRT1_WP extended (y:232–280), GEO_LRT1 extended with real coords, TRACK_LRT1 extended
- SVG station circles added, lrt1Path polyline extended, viewBox updated to 305 height
- VIEWBOXES.all/lrt1 constants updated
- Linear route hscroll auto-extends from SVG_LRT1_WP

### B. Map Bounds Extended ✅
- NCR_BOUNDS SW: (14.43, 120.88), NE: (14.80, 121.22) — shows Antipolo (LRT-2) and Dr. Santos (LRT-1 south)

### C. Timestamp Two-Line ✅
- #mapUpdateStamp uses innerHTML with <br> to show "Map updated" / "X ago" on two lines
- Removed white-space:nowrap from CSS

### D. Mode Pill Narrowed ✅
- .peek-mode-pill max-width:68px, padding reduced, font-size:8px — no longer reaches dividers

### E. Notification Bubble Zoom Threshold ✅
- updateNotifMapMarkerVisibility() now requires zoom >= 14 (was 13)

### F. Driver Mode + Transit Follow ✅
- transitFollowMode() calls showTransitLines() + showTrainDots() when currentMode==='driver'

### G. Forecast Search Bar + Swipe ✅
- City pill tabs replaced with search input + suggestions dropdown (autocomplete)
- Touch swipe left/right on pred card cycles through cities
- "← swipe to browse cities →" hint text added

### H. Guest View Permanent Removal ✅
- applyUserProfile() removes #profile-guest-view from DOM permanently via .remove()

### I. Logo Splash Animation ✅
- #splashAnim div shown for 1.8s after startJourney() — MMITS + Hiraya Transit + Bagong Galaw
- Gold glow + staggered text reveal animations
- Auto-fades out after 1.8s

### J. Commuter Mode Hardened ✅
- stopCongestionUpdates() called before clearRoadCongestion() to prevent redraw race
- Removed duplicate stopCongestionUpdates() call

---

## 17. New Features — v16.4 (2026-05-02)

### K. Splash Dark Mode Fix ✅
- Theme (light/dark) is now applied AFTER splash animation completes, not before.
- `.splash-anim` has `background:#080810 !important;color:#fff !important` so it always renders dark.
- SOS `#ctrlZone` hidden during splash, restored after.

### L. Definitive Notification Bubble Fix ✅
- Added CSS: `#map-container.notifs-hidden .notif-bubble-marker { display:none !important }`.
- `updateNotifMapMarkerVisibility()` now toggles `notifs-hidden` class on `#map-container` when zoom < 15, bypassing Leaflet layer management race conditions.
- `placeNotifMapMarkers()` adds `notifs-hidden` initially; `notif-bubble-marker` class added to divIcon.

### M. Commuter Mode Road Polygons OFF ✅
- Confirmed: `selectMode('commuter')` already hardened with `clearRoadCongestion()`, `congestionToggleOn=false`, road toggle UI set to OFF.

### N. Driver/Commuter Pill Reduced ✅
- `max-width: 58px`, `padding: 3px 7px`, icon `9px`, text `8px`, gap `3px`.

### O. Number Coding Date ✅
- Added `#numCodingDate` span inside `modeContent.driver` coding-widget.
- `updateNumCodingDate()` sets current date in "Month DD, YYYY" format on DOMContentLoaded.

### P. Predictive Congestion Gallery Swipe Animation ✅
- CSS classes: `swipe-left`, `swipe-right`, `swipe-enter-left`, `swipe-enter-right` on `#predCard`.
- Content update extracted to `updateForecastContent(city)`.
- `renderForecastCity(city, direction)` applies lean + exit/enter animation matching gallery swipe direction.
- Touch swipe handler updated to pass `'left'`/`'right'` direction to `renderForecastCity`.

### Q. Transit All-subtab Zoom/Pan ✅
- `initTransitAllZoom()` adds wheel zoom (0.7× – 3×) and touch drag pan on `#transitMapWrap`.
- Zoom buttons (+/−/Reset) added above SVG in transit all-routes view.
- CSS: `.transit-all-wrap`, `.transit-all-svg`.
- `#transitSVG` renamed to `#transitAllSvg`; all JS and CSS references updated.
- `initTransitAllZoom()` called in DOMContentLoaded.

### R. Linear Route Card Design Overhaul ✅
- Header added to each linear route card: train icon + "Route Map" (left), 3 logo circles/diamond (right).
- Glowing colored banner below header: green/LRT-1, blue/LRT-2, gold/MRT-3.
- CSS: `.lr-card-header`, `.lr-card-header-left`, `.lr-card-header-logos`, `.lr-logo-circle`, `.lr-logo-diamond`, `.lr-banner`, `.lr-banner-lrt1`, `.lr-banner-lrt2`, `.lr-banner-mrt3`, `.lr-banner-text`.

### S. Two Trains With Direction Arrows ✅
- Train A labeled `▲` (northbound), Train B labeled `▼` (southbound) in linear route view.
- Train B rendered at `opacity:0.8` to visually distinguish secondary train.
- `title` attribute added for accessibility.

### Key files modified
- `MMITS Hiraya Transit.html` — all changes inline

---

## 18. New Features — v16.5 (2026-05-07)

### A. Commuter-Focused Rebranding ✅
- Onboarding tagline changed: "Bagong Galaw · Metro Manila Intelligent Transit" → "Your Commuter Map for Metro Manila"
- Splash tag changed: "Bagong Galaw" → "THE Commuter Map · Metro Manila"
- Status bar left label: "Metro Manila" → "Commuter Map · NCR"
- Auth overlay subtitle: "Hiraya Transit — Metro Manila" → "Hiraya Transit · Student Commuter Edition"
- `modeBannerContent.commuter.items[0]` updated to: "THE commuter map for Metro Manila"

### B. Driver Mode Fully Removed ✅
- Driver pill removed from onboarding role selector (kept: Commuter, Both)
- `#pill-driver` seg-pill removed from bottom sheet mode switcher; `#pill-commuter` now starts active
- `currentMode` global state now initializes to `'commuter'` (was `'driver'`)
- `startJourney()` role fallback changed from `'driver'` to `'commuter'`; `'both'` role maps to `'custom'` mode
- `modeMetrics`, `modeContent`, `modeBannerContent` objects all had `driver` key removed
- `selectMode()`: driver icon/name removed, driver layer preset branch removed, always uses `SIMULATED` traffic provider
- `applyModeLayerPresets()`: driver branch removed
- `transitFollowMode()`: always calls `showTransitLines()` + `showTrainDots()` (no mode check)
- Initial DOMContentLoaded loads `modeContent.commuter` (was `modeContent.driver`)
- Post-init `if(currentMode==='driver')hideTrainDots()` line removed

### C. Commuter's Choice Route Optimization ✅
- `COMMUTER_CHOICE_MODES` object: three modes — **Efficiency** (⚡ teal), **Convenience** (🛋️ purple), **Budget** (💰 green)
- `let commuterChoiceMode = 'efficiency';` global state
- `setCommuterChoice(mode)` function: updates `.cc-btn.active` state, shows toast, triggers fare card refresh
- Three-button widget prepended to commuter `modeContent` HTML
- CSS `.cc-btn.active { filter:brightness(1.3); box-shadow:inset 0 0 0 1px currentColor; }`

### D. University-Scoped Prototype ✅
- `NCR_DESTINATIONS` extended with 12 university keys: `ust`, `up diliman`, `up manila`, `admu`, `ateneo`, `dlsu`, `la salle`, `feu`, `ue manila`, `mapua`, `pup`, `nu manila`
- `UNIVERSITY_FARES` object added with 12 entries (rail line, fare, last-mile mode/cost, jeepney route/fare)
- Commuter nudge card updated: title "🎓 Student Route Suggestion", UST/UP Manila fare example, "View University Routes →" action
- Onboarding destination placeholder: "e.g. UST, UP Diliman, DLSU, Ateneo…"
- Auth overlay subtitle: "Hiraya Transit · Student Commuter Edition"

### E. Route Fare Breakdown Card ✅
- `renderRouteFareCard(destKey)` function: generates a glass card with rail fare, last-mile fare, total, and all-mode comparison row
- `<div id="routeFareCard">` mount point placed between Commuter's Choice widget and nudge card in commuter content
- `doRouting()` stores `window._lastResolvedDest` and calls `renderRouteFareCard()` after every route resolution
- `selectMode('commuter')` re-renders fare card if a destination was previously resolved (100ms delay)
- Total displayed in active Commuter's Choice mode color; all three mode totals shown in comparison row

### Agent Potato Data ✅
- `agent-potato-data/university_data.json` — Metro Manila university coordinates + nearest LRT/MRT station + 2024 fares
  - Source: OSM knowledge base + LRTA/MRTC published fare matrices (2024)
  - Key correction: PUP coords updated to [14.5972, 121.0079] (Sta. Mesa) and nearest station is LRT-2 Anonas

### Key files modified
- `MMITS Hiraya Transit.html` — all changes inline
- `agent-potato-data/university_data.json` — new file

---

## 19. New Features — v16.6 (2026-05-08)

### A. University Search Dropdown ✅
- Map search bar (`#univSearchInput`) triggers `#univSearchDropdown` showing filtered university list
- `UNIVERSITY_LIST` array: 10 entries with `{key, name, short, icon, line, station}` for dropdown display
- `initUniversitySearch()` wires input + dropdown filtering + keyboard/click selection
- Selecting a university opens `#routePrefModal` before building any route

### B. Route Preference Modal ✅
- `#routePrefModal` — glass modal with 3 large cards: ⚡ Efficiency, 🛋️ Convenience, 💰 Budget
- Preference selection calls `buildUniversityRoute(univKey, pref)` and closes modal
- Modal has an `×` dismiss button; ESC key also closes it

### C. Multi-Leg University Route Builder ✅
- `buildUniversityRoute(univKey, pref)` orchestrates 3-leg routing:
  1. Walk leg: user position → nearest LRT/MRT station (road-snapped polyline)
  2. Transit leg: station → alight station (Leaflet polyline along TRACK_LRT1/2/MRT3 coords)
  3. Final walk leg: alight station → university gate (UNIVERSITY_WALK_PATHS waypoints)
- Preference multipliers from `COMMUTER_CHOICE_MODES` applied to ETA calculation
- `UNIVERSITY_WALK_PATHS` constant: 10 universities with `{line, stationName, walk:[...]}` per entry
- Walk waypoints follow actual streets; station → gate paths hand-matched to real Manila roads

### D. Route Instructions in Bottom Sheet ✅
- `renderRouteInstructions(route)` builds step-by-step HTML inside `.sheet-body`
- Each step shows: icon, action text, duration/distance chip
- Transit steps show line name and direction with colored badge
- Route header: destination name + traffic legend (green/yellow/orange/red key)

### E. ETA Badge ✅
- `#routeEtaBadge` — glass pill anchored above bottom nav; shows "ETA ~X min · Y.X km"
- Appears on route build, dismissed when `clearUniversityRoute()` is called
- ETA adjusts based on preference mode multiplier and simulated traffic

### F. Clear Route ✅
- `clearUniversityRoute()` removes all route polylines, destination marker, ETA badge, and resets bottom sheet

### Key files modified
- `MMITS Hiraya Transit.html` — all changes inline
- `agent-potato-data/university_walk_paths.json` — new file (10 universities, 10–12 waypoints each)

---

## 20. New Features — v16.7 (2026-05-08)

### A. Road-Snapped Routing ✅
- `buildRoadAlignedPath(from, to)` replaces straight-line walk legs
- Algorithm: collect road coordinate points within a corridor (90% of straight-line distance + 10m buffer) from `ROAD_CONGESTION_DATA`, snap endpoints to nearest road point, then greedy-walk toward destination choosing road-network edges
- Max 18 greedy steps; falls back to straight line if < 2 road points in corridor
- Walk-to-station and final-walk legs both use this function

### B. Traffic-Colored Walk Legs ✅
- `renderTrafficColoredPath(pts, weight, dashArray)` splits a polyline into individual 2-point segments
- Each segment colored by `nearestTrafficSpeed()` → `speedToTrafficState()` lookup
- Walk legs render with `weight:3, dashArray:'6 4'`; transit legs are solid with higher weight

### C. Rich Destination Marker ✅
- `.udc-card` Leaflet divIcon replaces the simple gold pill marker
- Card shows: university emoji icon, university name, nearest station chip (line color), fare chip (₱ amount), ETA chip
- CSS arrow pointer anchors card to exact coordinate
- Z-index placed above route polylines

### D. Corrected University Coordinates ✅
- `NCR_DESTINATIONS` and `UNIVERSITY_WALK_PATHS` updated:
  - UP Manila: `[14.5836, 120.9882]` (near LRT-1 UN Avenue, PGH area — was ~2.5km north)
  - PUP: `[14.5993, 121.0102]` (Sta. Mesa campus — now matches LRT-2 Pureza walk)
  - FEU walk path corrected to start from LRT-2 Recto `[14.6036, 120.9831]` (not Legarda)
  - NU Manila walk path corrected to start from LRT-2 Recto `[14.6036, 120.9831]` (not Legarda)

### E. Traffic Legend in Route Header ✅
- Route instructions header includes a 4-state traffic legend row: 🟢 Flow · 🟡 Slow · 🟠 Heavy · 🔴 Stop

### F. Agent Potato Walk Path Verification ✅
- `agent-potato-data/university_walk_paths.json` updated with Agent Potato's verified paths:
  - All 10 universities, 10–16 waypoints each
  - Street descriptions verified against real Manila road layout
  - Paths follow named streets (España Blvd, Katipunan Ave, Nicanor Reyes St, Muralla St, etc.)

### Key files modified
- `MMITS Hiraya Transit.html` — all changes inline
- `agent-potato-data/university_walk_paths.json` — updated with verified paths

*End of CLAUDE.md — MMITS Hiraya Transit v16*
