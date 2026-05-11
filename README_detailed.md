# Field Diary — Metal Detecting Field Log

A single-file progressive web app for recording and managing metal detecting finds in the field. Works on Android (Chrome), desktop Chrome/Edge, and any device with a modern Chromium-based browser. All data is stored as real files in a folder you choose — no accounts, no cloud service, no dependencies.

---

## Table of Contents

- [Background](#background)
- [Overview](#overview)
- [Features](#features)
- [Screenshots & Layout](#screenshots--layout)
- [Data Model](#data-model)
- [Storage Architecture](#storage-architecture)
- [Find Types](#find-types)
- [Browser Requirements](#browser-requirements)
- [Deploying via GitHub Pages](#deploying-via-github-pages)
- [Using the App](#using-the-app)
- [Export & Backup](#export--backup)
- [Development Notes](#development-notes)

---

## Background

This app was built for a new hobby — metal detecting in the historically rich Plzeň Region of the Czech Republic. The area sits at a remarkable intersection of medieval and modern history: Hussite-era fortifications, old trade routes through the Brdy hills, and traces of the 1945 American liberation of western Bohemia.

The detector in use is a **Minelab Equinox 900**, a multi-frequency machine whose Target ID (TID) system is central to the app's data model. The TID number — a value roughly from 1 (ferrous) to 99 (high-conductor non-ferrous) — is the primary field classifier. Experience quickly shows that TID alone is insufficient: the same object type in different soil conditions or orientations produces a range of readings, and signal stability and repeatability carry as much diagnostic information as the number itself. The app was designed to capture all of this.

Metal detecting in the Czech Republic is legal on private land with the landowner's permission and on certain public lands, but finds of archaeological significance must be reported to the regional heritage authority (*krajský památkový ústav*). The app is designed to support this obligation — every find is precisely geolocated and documented with photos and notes that make reporting straightforward.

---

## Overview

Field Diary is a purpose-built logging tool for metal detecting sessions. It captures everything that matters at the moment of a find: GPS coordinates, photos, detector Target ID (TID) readings, signal characteristics, audio memos, depth and soil context, and a preliminary identification with historical notes.

The app is a single HTML file with no build step, no framework, and no external runtime dependencies beyond Google Fonts. It uses the browser's [File System Access API](https://developer.mozilla.org/en-US/docs/Web/API/File_System_API) to read and write directly to a folder on your device or Google Drive. Your data lives in plain files you own.

---

## Features

### Field capture
- **GPS location** — one-tap acquisition with accuracy readout (±m); auto-starts when opening a new find
- **Photos** — multiple full-resolution images per find, stored as JPEG/PNG files; thumbnail grid in the list, full-screen lightbox viewer in the detail view
- **Target ID (TID)** — manual min/max range entry with stability checkbox; optional photo of the detector display for reference
- **Signal quality** — 1–5 dot ratings for stability and repeatability; free-text signal notes (tone, width, directional behaviour)
- **Audio** — separate voice memo and detector signal recordings (WebM/Opus); requires HTTPS (see [Browser Requirements](#browser-requirements))
- **Context** — depth in cm, terrain type (ploughed field, garden, meadow, etc.), and soil description
- **Identification** — preliminary ID text and free-text historical/descriptive notes

### Find types
Three recording modes for different situations — see [Find Types](#find-types) for details.

### Sites
Named locations that group related finds. Each site can have a GPS coordinate, a description, and a find count. Sites appear as filter pills on the finds list for quick scoping.

### Reference library
Any find can be promoted to the personal TID reference library. Calibration-type entries are added automatically. The reference view provides a quick-lookup database of known signal signatures from your own detector and local soil conditions.

### Desktop layout
On screens ≥ 768 px wide the app switches to a sidebar layout:
- Left sidebar with app title, active folder name, navigation, and a live summary (find count, site count, reference count, latest find)
- Two-column finds grid (auto-fill, max 860 px)
- Centred modal dialogs instead of bottom sheets
- Full-screen photo lightbox with prev/next navigation and keyboard control (← → Esc)

### Export / import
- **JSON** — full backup of the diary database (finds + sites); importable back in
- **CSV** — spreadsheet-friendly export of all find fields

---

## Screenshots & Layout

```
Desktop (≥ 768 px)                    Mobile (< 768 px)
┌──────────┬────────────────────┐     ┌────────────────────┐
│ Field    │  Finds             │     │ Field Diary    ⬇ ⚙ │
│ Diary    │  ┌──────┐ ┌──────┐ │     ├────────────────────┤
│ 📁 folder│  │ Find │ │ Find │ │     │  [All] [Site A]    │
│          │  │ card │ │ card │ │     │  ┌────────────────┐ │
│ ⌂ Finds  │  └──────┘ └──────┘ │     │  │   Find card    │ │
│ ◉ Sites  │  ┌──────┐ ┌──────┐ │     │  └────────────────┘ │
│ ☆ Ref    │  │ Find │ │ Find │ │     │  ┌────────────────┐ │
│          │  └──────┘ └──────┘ │     │  │   Find card    │ │
│ Summary  │                    │     │  └────────────────┘ │
│ 2 finds  │                    │     ├────────────────────┤
│ 2 sites  │                    │     │ ⌂ Finds ◉ Sites ☆  │
│          │                    │     └──────────── [+] ───┘
│ + New    │                    │
└──────────┴────────────────────┘
```

---

## Data Model

All data is persisted in `diary.json` in the root of the connected folder. The schema is versioned (`"version": 2`).

### Top-level structure

```json
{
  "version": 2,
  "sites": [ ...Site ],
  "finds": [ ...Find ]
}
```

### Site

```json
{
  "id": "demo_site_1746920414783",
  "name": "North field",
  "description": "Ploughed field, medieval settlement nearby.",
  "coords": {
    "lat": 50.0755,
    "lon": 14.4378,
    "acc": 5
  },
  "created": "2026-05-10T22:20:14.783Z"
}
```

| Field | Type | Description |
|---|---|---|
| `id` | string | Unique ID — `demo_site_` prefix for the seeded entry, `Date.now()` for user-created entries |
| `name` | string | Display name |
| `description` | string | Free-text notes, historical context |
| `coords` | object \| null | GPS coordinates. `null` if no location was recorded |
| `coords.lat` | number | Latitude in decimal degrees |
| `coords.lon` | number | Longitude in decimal degrees |
| `coords.acc` | number | GPS accuracy in metres |
| `created` | ISO 8601 string | Creation timestamp |

### Find

```json
{
  "id": "demo_seed",
  "siteId": "demo_site_1746920414783",
  "timestamp": "2026-05-10T22:20:14.783Z",
  "findType": "field",
  "coords": {
    "lat": 50.0755,
    "lon": 14.4378,
    "acc": 5
  },
  "photoRefs": [
    "find_demo_seed_p1746920466391_0.jpg",
    "find_demo_seed_p1746920478658_0.jpg"
  ],
  "tidDisplayRef": null,
  "audioRefs": {
    "voice": "find_demo_seed_voice_1746920493357.webm",
    "detector": null
  },
  "tid": {
    "min": 78,
    "max": 85,
    "stable": true
  },
  "signal": {
    "stability": 5,
    "repeatability": 5,
    "notes": "Very clean, consistent from all 4 directions. Classic high-conductor flat-disc response."
  },
  "context": {
    "depth": 8,
    "foundIn": "Garden",
    "soil": "Agricultural loam, moderate mineralisation"
  },
  "identification": {
    "preliminary": "Example find — .50 BMG cartridge case (1944)",
    "description": "Demo record showing all available fields. Replace with your own find data."
  },
  "isReference": true,
  "editedAt": "2026-05-11T11:16:33.356Z"
}
```

| Field | Type | Description |
|---|---|---|
| `id` | string | Unique ID — `demo_seed` for the seeded entry; `Date.now()` for user entries |
| `siteId` | string \| null | Foreign key → `Site.id`. `null` if no site assigned |
| `timestamp` | ISO 8601 string | Time of find creation |
| `findType` | `"field"` \| `"backfill"` \| `"calibration"` | Recording mode — see [Find Types](#find-types) |
| `coords` | object \| null | GPS coordinates (same shape as Site.coords). `null` for calibration and location-less backfill entries |
| `photoRefs` | string[] | Filenames of photo files inside the `photos/` subfolder |
| `tidDisplayRef` | string \| null | Filename of an optional photo of the detector display, stored in `photos/` |
| `audioRefs.voice` | string \| null | Filename of voice memo in `audio/` |
| `audioRefs.detector` | string \| null | Filename of detector signal recording in `audio/` |
| `tid.min` | number \| null | Minimum Target ID reading |
| `tid.max` | number \| null | Maximum Target ID reading (equals `min` for a single stable value) |
| `tid.stable` | boolean | Whether the signal was consistent / non-jumping |
| `signal.stability` | number (1–5) | Dot rating: how steady the signal was |
| `signal.repeatability` | number (1–5) | Dot rating: how consistently the signal repeated from multiple directions |
| `signal.notes` | string | Free-text signal description |
| `context.depth` | number \| null | Recovery depth in centimetres |
| `context.foundIn` | string | Terrain type (enum: `Ploughed field`, `Garden`, `Driveway`, `Meadow`, `Stream bank`, `Gravel bar`, `Forest`, `Other`) |
| `context.soil` | string | Soil type and condition notes |
| `identification.preliminary` | string | Best-guess identification at time of find |
| `identification.description` | string | Extended notes: historical context, provenance, research |
| `isReference` | boolean | Whether the find appears in the TID reference library |
| `editedAt` | ISO 8601 string \| undefined | Last edit timestamp (added on save via the Edit flow) |

---

## Storage Architecture

The app uses the **File System Access API** (`window.showDirectoryPicker`) and stores everything as real files in a user-chosen folder:

```
MyDetectingFolder/
├── diary.json          ← all finds and sites (human-readable JSON)
├── photos/
│   ├── find_<id>_p<ts>_0.jpg     ← find photos (JPEG or PNG)
│   └── find_<id>_disp.<ext>      ← optional TID display photo
└── audio/
    ├── find_<id>_voice_<ts>.webm  ← voice memos
    └── find_<id>_sig_<ts>.webm    ← detector signal recordings
```

**File naming convention:**

| File | Pattern | Notes |
|---|---|---|
| Find photo | `find_<findId>_p<timestamp>_<index>.<ext>` | Index is position in photoRefs array |
| TID display photo | `find_<findId>_disp.<ext>` | Optional reference photo of detector screen |
| Voice memo | `find_<findId>_voice_<timestamp>.webm` | WebM/Opus container |
| Detector signal | `find_<findId>_sig_<timestamp>.webm` | WebM/Opus container |

The folder handle is persisted in **IndexedDB** (`fd_h` database, `h` object store, key `'dir'`) so the app can silently reconnect on reload without asking the user to re-select the folder. Chrome will occasionally require re-granting the permission after a browser restart; the app detects this state (`queryPermission` returns `'prompt'`) and shows a one-tap reconnect banner.

> **Tip:** Point the app at a folder inside Google Drive on your desktop and you get automatic cloud backup with no extra setup.

---

## Find Types

| Type | Icon | GPS | Use case |
|---|---|---|---|
| `field` | 📍 | Auto-acquired | Normal in-field find. GPS starts automatically when the form opens |
| `backfill` | ✎ | Manual entry | Recording a find after the fact. Accepts pasted coordinates, Google Maps links, or Mapy.cz links |
| `calibration` | 🎯 | None | Known test targets (a coin on a bench, a nail in the garden). Automatically added to the Reference Library. No location recorded |

---

## Browser Requirements

| Feature | Requirement |
|---|---|
| File storage | Chrome ≥ 86 or Edge ≥ 86 (File System Access API) |
| GPS | Any browser with `navigator.geolocation` |
| Camera / photos | Any browser with `<input type="file" accept="image/*">` |
| Audio recording | Chrome/Edge **over HTTPS** (MediaRecorder + getUserMedia blocked on `file://`) |
| Lightbox / full UI | Any modern browser for viewing; Chrome/Edge required for saving |

Firefox and Safari do not support the File System Access API and cannot save data. The app detects this on load and shows a clear error.

Audio recording requires a secure context (HTTPS or localhost). When running the HTML file directly from the local filesystem (`file://`), audio recording is disabled. All other features — GPS, photos, saving, export — work on `file://`.

---

## Deploying via GitHub Pages

GitHub Pages is the recommended zero-cost hosting option. It gives you a stable HTTPS URL, which unlocks audio recording on mobile.

### Step 1 — Create a repository

1. Go to [github.com](https://github.com) and sign in (create a free account if needed)
2. Click **New repository**
3. Name it `field_diary` (or any name you prefer)
4. Set visibility to **Public** (required for free GitHub Pages)
5. Click **Create repository**

### Step 2 — Upload the app file

1. In the new repository, click **Add file → Upload files**
2. Drag in `index.html` (the single app file)
3. Commit with a message like `Initial deploy`

### Step 3 — Enable GitHub Pages

1. Go to **Settings → Pages** (left sidebar)
2. Under **Source**, select **Deploy from a branch**
3. Choose branch `main` (or `master`), folder `/ (root)`
4. Click **Save**

GitHub will build and deploy. After about 60 seconds your app is live at:

```
https://<your-username>.github.io/<repository-name>/
```

### Step 4 — Open on your phone

Open the URL above in **Chrome for Android**. On first visit:

1. Tap the **⚙ storage** badge or the gear icon
2. Tap **Connect folder** and choose a folder (or create one called `Detecting`)
3. The app is ready — all features including audio recording are active

### Updating the app

When a new version of `index.html` is available:

1. Go to your repository on GitHub
2. Click on `index.html` → **Edit** (pencil icon), or use **Add file → Upload files** to replace it
3. Commit the change — GitHub Pages redeploys automatically within ~60 seconds

Your data is not affected by app updates. `diary.json` and the `photos/` and `audio/` folders live in the folder you connected, completely separate from the app file.

### Custom domain (optional)

If you have a domain, you can point it at GitHub Pages via a CNAME record. See the [GitHub Pages custom domain docs](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site) for details.

---

## Using the App

### First run

On first open the app shows a folder connection dialog. Choose or create a folder on your device (or inside Google Drive/OneDrive for automatic cloud sync). The app creates `diary.json`, `photos/`, and `audio/` inside it.

A demo find is added automatically on first run so the interface is not empty. It demonstrates all app fields — TID range, signal quality ratings, depth, soil, photos, audio, and identification — and can be deleted once you have your own finds recorded.

### Recording a find

1. Tap **+ New Find** (desktop: sidebar button; mobile: gold FAB button)
2. Choose the find type (Field / Backfill / Calibration)
3. For **Field finds**: GPS acquires automatically; wait for the green ± accuracy reading
4. Add **photos** — tap the ＋ tile to open camera or file picker; multiple photos supported
5. Enter the **TID** range (min–max) or tick *Stable* for a single consistent reading; optionally photograph the detector display
6. Rate **signal quality** — tap the dots for stability (how steady) and repeatability (how consistent from all directions); add signal notes
7. Record **audio** — voice memo for observations, or detector signal for later reference (requires HTTPS)
8. Fill in **context**: depth, terrain type, soil description
9. Write the **identification**: preliminary name and any historical/research notes
10. Tap **✓ Save Find**

### Viewing a find

Tap any find card to open the detail view. Photos display as a thumbnail grid — tap any photo to open the full-resolution lightbox. Navigate between photos with the ‹ › arrows or ← → keyboard keys. Press Escape or tap outside to close.

### Sites

Sites group related finds by location. Create a site via **Sites → + Add Site**, then assign finds to it when recording. On the Finds view, site pills appear as filters at the top.

### Reference library

Open any find → tap **☆ Add to Reference**. The find appears in the Reference view with its TID and signal notes. Calibration-type entries are added automatically. Use this to build a personal lookup table of known signal signatures for your specific detector and local mineralisation.

### Editing a find

Open the detail view → tap **✏ Edit**. All fields except GPS coordinates and find type are editable. New photos can be added; existing photos can be removed. Audio can be replaced.

---

## Export & Backup

### Manual backup

The `diary.json` file in your connected folder is the single source of truth. Copy the entire folder (including `photos/` and `audio/`) to back up everything.

### JSON export

Settings (⚙) → **Export → ⬇ JSON** — downloads a `detecting_diary_YYYY-MM-DD.json` snapshot of the database. This file can be imported back via **⬆ Import JSON** on any device to merge records.

### CSV export

Settings (⚙) → **Export → ⬇ CSV** — downloads a spreadsheet-compatible file with one row per find. Columns: Date, Site, TID Min, TID Max, Stable, Lat, Lon, Acc m, Depth cm, Found In, Soil, Stability, Repeatability, Signal Notes, ID, Description.

### Migrating to a new device

1. Copy the entire folder (diary.json + photos/ + audio/) to the new device or to a shared location
2. Open the app on the new device → Connect folder → select the copied folder
3. All finds, photos, and audio are immediately available

---

## Development Notes

### Architecture

The entire application is a single `index.html` file (~1,500 lines). No build step, no bundler, no npm. Dependencies:

- **Google Fonts** (Playfair Display, DM Mono, Instrument Sans) — loaded from CDN; the app is fully functional without them (system fonts fall back gracefully)
- **File System Access API** — Chrome/Edge only; required for all persistent storage
- **MediaRecorder API** — for audio recording; requires HTTPS
- **Geolocation API** — for GPS; works on `file://` and HTTPS

### Key design decisions

**Single file.** Everything in one HTML file makes deployment trivial (one file upload, no server config, no asset paths) and ensures the app always deploys atomically.

**Real files, not IndexedDB.** Photos and audio are stored as real files in the connected folder — not as base64 in a database. This means you can open them in any app, browse them in Finder/Explorer, and back them up with any file sync tool.

**No network requests for data.** Once loaded, the app makes zero network requests. Offline operation is complete (no service worker needed — there is nothing to fetch).

**History API integration.** All overlays (detail view, edit view, modals, lightbox) push a browser history state. The Android back button and desktop Back button close the current overlay rather than navigating away from the app.

### Browser storage for the folder handle

The `FileSystemDirectoryHandle` for the connected folder is persisted in IndexedDB under database `fd_h`. This allows the app to silently reconnect on reload. Chrome requires the permission to be re-granted after a cold browser restart — the app detects the `'prompt'` state and shows a reconnect banner without requiring the user to re-select the folder.

### Coordinate formats accepted in backfill mode

The manual location field accepts:
- Decimal coordinates: `50.0755, 14.4378`
- Google Maps full URL (extracts `?q=lat,lon` or `@lat,lon` patterns)
- Mapy.cz URL (extracts `x=lon&y=lat` parameters)
- Shortened `goo.gl` links are detected and rejected with a helpful message

---

## Licence

MIT — do whatever you like with it.
