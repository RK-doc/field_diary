# Field Diary 🔍

> A free, private field log for metal detectorists. No account. No cloud. Your data stays yours.

**[▶ Try it live](https://rk-doc.github.io/field_diary/)** &nbsp;·&nbsp; Works on Android & desktop Chrome

---

## What is it?

Field Diary is a web app that runs entirely in your browser and saves everything — finds, photos, audio, GPS — as real files in a folder on your own device or Google Drive. There is no server, no subscription, and no one else can see your data.

It was built by a detectorist, for detectorists, with the specific challenges of field recording in mind: capturing a good TID reading takes seconds, but so does losing the context around it. Field Diary makes it fast to record everything at the moment of the find, so nothing is forgotten by the time you get home.

---

## What can it record?

For each find you can capture:

| Field | Details |
|---|---|
| 📍 **GPS location** | Auto-acquired with accuracy readout; or paste a Google Maps / Mapy.cz link for backfill entries |
| 📷 **Photos** | Multiple full-resolution images; tap any thumbnail to view full-screen |
| 🎯 **Target ID (TID)** | Min–max range, stability flag, optional photo of the detector display |
| 📶 **Signal quality** | 1–5 dot ratings for stability and repeatability; free-text signal notes |
| 🎙 **Audio** | Voice memo and/or detector signal recording |
| 📏 **Context** | Depth in cm, terrain type, soil conditions |
| 📝 **Identification** | Preliminary ID and historical / research notes |

---

## Three find types

| Type | When to use |
|---|---|
| 📍 **Field find** | Normal in-field find — GPS acquired automatically |
| ✎ **Backfill** | Recording a find after the fact — enter location manually |
| 🎯 **Calibration** | Known test targets — automatically added to your reference library |

---

## Reference library

Mark any find as a reference entry. Over time this builds into your personal TID lookup table — calibrated to your own detector, coil, and local soil conditions. Much more useful than generic TID charts.

---

## Your data, your files

```
YourFolder/
├── diary.json        ← all records (human-readable)
├── photos/           ← full-resolution images
└── audio/            ← voice memos & signal recordings
```

Point the app at a Google Drive folder and you get automatic cloud backup with no extra setup. Export to JSON (full backup) or CSV (spreadsheet) any time.

---

## Desktop & mobile

| | Mobile (Android Chrome) | Desktop (Chrome / Edge) |
|---|---|---|
| Layout | Bottom nav, bottom-sheet modals, FAB button | Sidebar nav, centred dialogs, keyboard shortcuts |
| Photos | Camera or gallery | File picker, full lightbox with ← → Esc |
| GPS | Full support | Full support |
| Audio | Full support (HTTPS required) | Full support (HTTPS required) |

---

## Getting started

### Option A — Just open it

Go to **[rk-doc.github.io/field_diary](https://rk-doc.github.io/field_diary/)** in Chrome on your phone or desktop. Connect a folder and start recording.

### Option B — Host your own copy

1. Fork or download this repository
2. Enable **GitHub Pages** (Settings → Pages → Deploy from branch `main`)
3. Your personal copy is live at `https://<your-username>.github.io/<repo-name>/`

Full step-by-step instructions are in [README_detailed.md](README_detailed.md).

---

## Browser support

The app requires **Chrome or Edge** (desktop or Android) for saving data — it uses the [File System Access API](https://developer.mozilla.org/en-US/docs/Web/API/File_System_API) which Firefox and Safari do not support. Audio recording additionally requires HTTPS (i.e. the hosted version, not a local file).

All other features — GPS, photos, export — work when running the file locally too.

---

## Works with any detector

The TID scale used in the app (1–99) matches the convention used by **Minelab Equinox**, **XP Deus**, **Garrett Apex**, and most modern VLF machines. If your detector uses a different scale, the fields are free-text and work just as well as notes.

---

## FAQ

**Is my data private?**
Yes. Nothing leaves your device except what you explicitly export. There is no backend, no analytics, no tracking.

**What happens if I lose my phone?**
If you pointed the app at a Google Drive or OneDrive folder, your data is already backed up. If not, use the JSON export regularly as a backup.

**Can I use it offline?**
Yes — once the page has loaded, it works completely offline. GPS, photos, and saving all work without a connection.

**Can I move my data to a new phone?**
Yes. Copy the folder (diary.json + photos/ + audio/) to the new device, open the app, connect that folder, and everything is there.

**Is there an iOS version?**
Not yet — Safari on iOS does not support the File System Access API. The app loads and is usable for browsing, but cannot save data.

---

## Licence

MIT — free to use, modify, and share.

---

*Built with ❤️ and a Minelab Equinox 900 somewhere in Bohemia.*
