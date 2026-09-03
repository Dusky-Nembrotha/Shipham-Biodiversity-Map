# Shipham Wild — Community Biodiversity Map

A simple, friendly map of conservation and biodiversity projects in and around
Shipham parish. It already shows, with **no setup at all**:

- **Protected wildlife sites (SSSIs)** — Natural England open data
- **Local nature reserves** — Natural England open data
- **Recent wildlife sightings** — live iNaturalist records
- **Community projects** — areas anyone can draw and describe, with photos

The data layers refresh as you pan and zoom the map.

---

## Live URL

```
https://dusky-nembrotha.github.io/Shipham-Biodiversity-Map/
```
---

## 1. Put it online (5 minutes, free)

The map is a single file, `index.html`, so GitHub Pages can host it directly.

1. Create a GitHub repository (e.g. `Shipham-Biodiversity-Map`).
2. Upload `index.html` to it (drag-and-drop on the GitHub website is fine).
3. Go to **Settings → Pages**.
4. Under *Build and deployment*: **Source: Deploy from a branch**, branch **main**, folder **/ (root)**, Save.
5. Wait a minute, then visit `https://YOUR-USERNAME.github.io/Shipham-Biodiversity-Map/`.

The map is now live and anyone can view it. Sightings, SSSIs and reserves all
work straight away.

> At this stage the **Add your project** button works in "preview mode": people
> can draw and describe a project, but it's only visible in their own browser
> until they refresh. To make contributions **shared and permanent** (with
> photos), do step 2 using the Google account you already have.

---

## 2. Turn on shared saving using your Google account (free, ~15 min)

GitHub Pages only serves files — it can't store what visitors submit, and you
**cannot** safely let strangers write to your Google Drive directly (that would
mean putting your Google password/keys in public code).

The proper way is a **Google Apps Script web app**. It's a tiny script that
lives in a Google Sheet and runs *as you* on Google's servers. The map sends it
each new project; it saves the details to the **Sheet** and the photos to a
**Drive folder**, and hands them back to the map. No keys are ever exposed, and
you don't sign up for anything new.

### a) Create the Sheet + script
1. Go to <https://sheets.google.com> and create a blank spreadsheet. Name it
   e.g. *Shipham Wild data*.
2. In that sheet: **Extensions → Apps Script**.
3. Delete any sample code, then paste the entire contents of **`Code.gs`**
   (included alongside this file). Click the **Save** icon.

### b) Deploy it as a web app
1. Top right: **Deploy → New deployment**.
2. Click the gear next to *Select type* → choose **Web app**.
3. Set:
   - **Execute as:** *Me*
   - **Who has access:** *Anyone*
4. Click **Deploy**. Google will ask you to authorise it — approve (you may need
   to click *Advanced → Go to (project) → Allow*; this is normal for your own script).
5. Copy the **Web app URL**. It ends in `/exec` and looks like
   `https://script.google.com/macros/s/AKfy…/exec`.

### c) Plug the URL into the map
Open `index.html`, find the **CONFIG** block near the bottom, and replace the
placeholder:

```js
const SCRIPT_URL = "https://script.google.com/macros/s/AKfy…/exec";
```

Save, re-upload `index.html` to GitHub, and you're done. Projects people add now
save to your Sheet and Drive and appear on the map for everyone. The script
creates a tab called **Projects** and a Drive folder called **Shipham Wild
Photos** automatically the first time someone submits.

> **If you change the script later**, redeploy with **Deploy → Manage
> deployments → (edit, pencil) → Version: New version → Deploy**, so the live URL
> picks up your changes.

---

## 3. Make it yours (optional)

All in `index.html`:

- **Re-centre the map** — edit the `SHIPHAM` line (`lat`, `lng`, `zoom`).
- **Colours** — the palette is at the top of the `<style>` block (`--moss`,
  `--heather`, etc.). The "Add your project" button and the community polygons
  share the heather colour on purpose, so the button matches what it creates.
- **Title and tagline** — in the `<div class="brand">` near the top of the page.

### Adding the parish boundary (optional)
The map currently centres on the village. If you'd like the exact civil-parish
boundary drawn on (ONS publishes it as open data), it can be added as another
toggle — just ask.

---

## Keeping open submissions tidy (optional)

Because anyone can add a project, a fully open map can occasionally attract
unwanted entries. Easy options:

- **Passcode:** in `Code.gs`, set `const SECRET = "somephrase";` and share that
  phrase with your contributors. (A small tweak to the form in `index.html` then
  sends it — ask if you'd like that wired up.)
- **Moderate:** projects live as rows in your Sheet. Deleting a row removes it
  from the map. You could also add an "approved" column and only show approved
  ones — ask if you want this.
- The **Drive folder** holds every uploaded photo, so you can review or remove
  images there too.

---

## Notes & limits

- Apps Script free quotas are generous and fine for a village map (thousands of
  reads/writes a day). No billing is involved.
- If photos ever fail to display, check that link-sharing isn't blocked on your
  Google account (some managed Workspace accounts restrict it); a personal Gmail
  account works out of the box.

## Data sources & credit

- SSSIs and Local Nature Reserves — © Natural England, Open Government Licence;
  contains Ordnance Survey data © Crown copyright and database right.
- Wildlife sightings — the iNaturalist community, via the iNaturalist API.
- Base maps — © OpenStreetMap contributors, © CARTO; aerial imagery © Esri.
