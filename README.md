# Shipham Wild — Community Biodiversity Map

A friendly map of nature and conservation across **Shipham, Rowberrow and Star**.
Anyone can explore what's already there, and add a project of their own.

**Live:** <https://dusky-nembrotha.github.io/Shipham-Biodiversity-Map/>

The whole map is one file, `index.html`, served by GitHub Pages. Editing that
file and pushing to `main` updates the live site within a minute or two.

---

## What's on the map

Everything below loads on its own — no sign-in, no setup.

### Always-on layers

| Layer | Source | Notes |
|---|---|---|
| **Parish boundary** | ONS open data | Shipham civil parish (Shipham, Rowberrow & Star). The map zooms to fit it on load. |
| **Protected wildlife sites** | Natural England | SSSIs reaching within **2.5 km** of the parish. |
| **Local nature reserves** | Natural England | LNRs within **2.5 km** — nearest are Cheddar Valley Railway Walk and Sladers Leigh. |
| **Recent wildlife sightings** | iNaturalist API | Up to 1,000 recent verifiable records in a fixed box around the parish, clustered. Falls back to iNaturalist's tile layer if the API can't be reached. |
| **Conservation projects** | This map's contributors | Areas and points people have drawn and described, with photos. |

### Context layers (off unless noted)

| Layer | Source | Notes |
|---|---|---|
| **Habitats** | `shipham-habitats.geojson` in this repo | Priority habitats, woodland and tree groups. Loaded only when switched on. |
| **Surface water** | Environment Agency WMS | Flood risk from surface water, drawn only over the Shipham area. |
| **Public rights of way** | `shipham-prow.geojson` in this repo | Footpaths, bridleways and byways. **On by default.** |
| **Nature recovery (LNRS)** | Somerset Wildlife Trust iShare WFS | Somerset Local Nature Recovery Strategy habitat priority areas, fetched live as GML and reprojected from British National Grid in the browser. Included where they reach within 500 m of the parish. |

### Base maps

**Satellite + OS** (default) · **OS map** · **Satellite** · **LiDAR** (Environment
Agency 1 m terrain hillshade) · **Old OS (1880s)** (National Library of Scotland
25-inch series).

### How layers are trimmed

Rather than loading the whole county, each layer keeps only features that reach a
buffer around the parish: **2.5 km** for SSSIs and nature reserves, **1.5 km** for
general overlays, **500 m** for LNRS. Features are kept **whole** — never clipped
in half at the buffer edge.

---

## Adding projects

The **Add your project** button lets anyone drop a point or draw an area, describe
it, and attach photos.

- Projects are saved to a **Google Sheet**, and photos to a **Google Drive folder**,
  through a Google Apps Script web app. This is already set up and live.
- Whoever adds a project can edit or delete it again from the same browser — a
  private token is kept in that browser's local storage.
- **Manage as admin** (bottom of the layers panel) asks for a password and then
  allows editing or deleting *any* project. The password is verified by the Apps
  Script on every change, so it can't be bypassed from the browser.

### Where the backend code lives

> The Apps Script source is **not in this repository**. It lives in the Google
> Sheet itself: open the Sheet → **Extensions → Apps Script**.

To change it: edit there, then **Deploy → Manage deployments → (pencil) → Version:
New version → Deploy**, so the existing `/exec` URL picks up the change. If you
create a *new* deployment instead, the URL changes and you must update
`APPS_SCRIPT_URL` in `index.html` to match.

### Moderating

Projects are rows in the Sheet — delete a row and it leaves the map. Every uploaded
photo is in the Drive folder, so images can be reviewed or removed there too.

---

## Configuration

All of it is in the `CONFIG` block near the top of the `<script>` in `index.html`:

```js
const OS_API_KEY      = "…";   // OS Data Hub key → OS Outdoor base map
const MAPTILER_KEY    = "…";   // sharper satellite imagery (falls back to Esri if absent)
const APPS_SCRIPT_URL = "…";   // Google Apps Script /exec URL — saving projects
const SHIPHAM = { lat:51.3138, lng:-2.7987, zoom:14 };   // initial view
```

If a key is missing the map degrades gracefully: without `MAPTILER_KEY` satellite
falls back to keyless Esri imagery; without `OS_API_KEY` the OS base map is
unavailable. Without `APPS_SCRIPT_URL` the map runs in preview mode, where added
projects appear only in the contributor's own browser.

### Keeping the keys safe

Both keys are visible to anyone who views the page — that's unavoidable for a
static site, so **restrict them by domain in the provider's dashboard** instead.

- **MapTiler** — already restricted to `dusky-nembrotha.github.io`. ✅
- **OS Data Hub** — currently **unrestricted**: the key works from any site, so
  anyone can lift it and spend the free-tier transactions. Add a referer
  restriction for `dusky-nembrotha.github.io` in the OS Data Hub project settings,
  and consider regenerating the key afterwards.

---

## Making it yours

- **Re-centre** — edit the `SHIPHAM` line above.
- **Colours** — the palette lives at the top of the `<style>` block:
  `--paper`, `--stone`, `--ink`, `--muted`, `--moss`, `--sage`, `--slate`,
  `--gorse`, `--heather`, `--line`.
- **Title and tagline** — in `<div class="brand">` near the top of the page.

---

## The data files

Two layers are served from this repo rather than a live service, because the
public services for them are slow or awkward to query from a browser.

| File | Features | Contents |
|---|---|---|
| `shipham-habitats.geojson` | 10,794 | Priority habitats, woodland, and tree groups |
| `shipham-prow.geojson` | 146 | Public rights of way |

**About the habitats file.** It originally held 25,985 features, but 15,183 of
those were single `Lone Tree` points from the National Forest Inventory canopy
data — 58% of the file, and thousands of near-invisible specks to draw at village
scale. Those have been dropped, and coordinates rounded to 5 decimal places
(~1 m, far finer than the source data warrants). Every priority-habitat polygon is
untouched. The file went from 8.2 MB to 4.5 MB (1.33 MB to 0.71 MB as GitHub Pages
actually serves it, gzipped), and from 25,985 shapes to draw down to 10,794.

If you ever need the lone trees back, the original file is in this repo's git
history.

---

## Notes & limits

- Apps Script's free quotas are generous — thousands of reads and writes a day,
  ample for a village map. No billing involved.
- If photos ever fail to display, check that link-sharing isn't blocked on the
  Google account. A personal Gmail account works out of the box; some managed
  Workspace accounts restrict it.
- The map needs an internet connection: Leaflet, Turf and proj4 load from CDNs,
  each with two fallback CDNs.

---

## Data sources & credit

- **SSSIs, Local Nature Reserves** — © Natural England, Open Government Licence;
  contains Ordnance Survey data © Crown copyright and database right.
- **Parish boundary** — Office for National Statistics licensed under the Open
  Government Licence; contains OS data © Crown copyright and database right.
- **Wildlife sightings** — the iNaturalist community, via the iNaturalist API.
- **Somerset Local Nature Recovery Strategy** — Somerset Wildlife Trust.
- **Surface water flood risk; LiDAR terrain** — © Environment Agency, OGL v3.
- **Historical OS 25-inch (1880s)** — National Library of Scotland, CC-BY.
- **Base maps** — © OpenStreetMap contributors, © CARTO; OS Outdoor © Crown
  copyright; satellite imagery © MapTiler, © Esri.
