[README_ZoneWidget.md](https://github.com/user-attachments/files/22957848/README_ZoneWidget.md)

# Zone Widget

A client‑side, single‑file web tool to turn **routes** or **regions** into **buffered polygons** and export them in common GIS formats—**WKT, GeoJSON, KML, and Shapefile (.zip)**. The app runs entirely in your browser; no server is required.

> **UI principle:** The **interface is always dark mode**. A map‑only toggle lets users switch the **basemap** (Dark ↔ Light) without changing the surrounding UI.

## Live Demo

- GitHub Pages: `https://hunterirvin.github.io/Hunter-Route-to-Polygon/`
- Entry file: `index.html` (or the latest packaged HTML from the repo)

---

## Features

- **Three input workflows**
  - **Google Maps URL:** Parse multi‑stop Directions links; auto‑geocode and route.
  - **Manual:** Click on the map to add **numbered pins** as waypoints; drag pins to re‑route.
  - **Regions:** Either **Find** an area by name/US ZIP (with polygon boundaries when available) or **Draw** a custom polygon directly on the map.
- **Buffering:** Create a buffer around the **Route** or **Region** using presets (10/25/50/100 m) and optional **Advanced buffering**:
  - Arc smoothness (**steps**) – default `6`
  - Simplify tolerance (**meters**) – default `7`
  - Coordinate precision (**decimals**) – default `5`
- **Outputs:** Choose **Geometry** (Buffer Polygon / Route Line / Region Polygon). Export to **WKT**, **GeoJSON**, **KML**, or **Shapefile (.zip)**.
- **Map‑only theme toggle:** Switches CARTO basemaps (Dark Matter / Positron). **Default = Dark.**
- **Polished interactions:**
  - **Type‑ahead** on waypoint and region inputs (Nominatim; debounced)
  - **Red** “Clear” buttons for destructive actions
  - Switching workflows **clears map layers** (route, buffer, region, pins)
  - Advanced panel **closed by default**

---

## How to Use

### 1) Google Maps URL → Route → Buffer → Export
1. Select **Google Maps URL**.
2. Paste a Directions link (multi‑stop supported; short links OK once expanded by the browser).
3. Click **Parse URL** to place pins; click **Run (Route/Region) & Buffer** to compute the route and buffer.
4. In **Output**, choose a **Geometry** and **Format**, then **Download**.

### 2) Manual Route (pins)
1. Select **Create Route Manually**.
2. Click the **map** to add pins in order (origin → … → destination). Drag pins to refine. Use ▲/▼ to reorder.
3. Click **Run** to build/rebuild the route and buffer.

### 3) Regions (Find ↔ Draw toggle)
1. Select **Regions**.
2. Use the **Find Region** mode:
   - Type a place or **US ZIP**; pick from suggestions; **Find Region** to load boundaries.
   - Or switch to **Draw Region**: click the map to sketch a polygon; close it to finish.
3. Click **Run** to buffer the region; export from **Output**.

### Map Theme
- The UI stays **dark**. The **Map: Dark/Light** toggle in the header switches **only** the basemap.

---

## Tech Stack & Architecture

- **Leaflet** – Map rendering, markers, tooltips.
- **Leaflet.draw** – Polygon sketching (Regions → Draw).
- **Turf.js** – Buffer, simplify, and precision rounding.
- **Nominatim (OpenStreetMap)** – Geocoding + polygon boundaries for Regions.
- **OSRM (public)** – Routing between ordered waypoints (driving profile).
- **wellknown** – WKT stringify for previews/exports.
- **shp‑write** – Browser‑side Shapefile creation as a ZIP (**loaded dynamically** with multi‑CDN fallback).

```mermaid
flowchart LR
  A[User] --> B[Leaflet UI]
  B --> C[State]
  C -->|Find/Geocode| D[Nominatim]
  C -->|Route| E[OSRM]
  C -->|Buffer/Simplify| F[Turf]
  C -->|WKT| G[wellknown]
  C -->|GeoJSON/KML| H[Exporter]
  C -->|Shapefile (.zip)| I[shp-write]
  C -->|Render layers| B
```

> **Projection:** WGS84 (EPSG:4326). Shapefile `.prj` is written accordingly.

---

## Shapefile Export – Notes

- The app **defer‑loads** `@mapbox/shp-write` on demand with **multiple CDN fallbacks**:
  1. `https://unpkg.com/@mapbox/shp-write@0.4.3/shpwrite.js` (primary)
  2. `https://unpkg.com/@mapbox/shp-write@latest/shpwrite.js`
  3. jsDelivr variants for `@mapbox/shp-write`
  4. Legacy `shp-write@0.3.2` (deep fallback only)
  5. Optional community mirrors (`@nickrsan`, `@simwrapper`) as network safety nets
- The resulting ZIP contains: **.shp, .shx, .dbf, .prj**.
- If all CDNs are blocked by CSP: build an **offline bundle** by inlining the UMD file into the HTML (replace dynamic loader with a `<script>` tag).

---

## Install / Run

### GitHub Pages
1. Ensure your repo’s **Pages** is set to serve from the branch containing `index.html`.
2. Commit the latest single‑file app as `index.html` to the repo root (or to `/docs` if that’s your Pages folder).

### Local
- Simply open the HTML file in a modern browser. (For best results with some APIs, serve via a local HTTP server.)

---

## Troubleshooting

- **Shapefile export fails with loader error**
  - Your network may block CDNs. Try again or inline the library into the HTML.
- **Short Google links** (`maps.app.goo.gl`) don’t parse
  - Open them once in a tab and paste the expanded URL.
- **Very complex polygons** produce large files
  - Increase **Simplify tolerance** (e.g., 10–25 m) and/or reduce **Precision** to shrink output.

---

## Privacy & Data
- No account, no backend: all processing happens **in your browser**. Requests go directly to **public OSRM/Nominatim**.

---

## Contributing
- Issues and PRs welcome. Add screenshots and reproduction steps.

## License
Add a license file (e.g., MIT) appropriate for your use.

