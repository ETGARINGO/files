# Reconciliation Data Checker

A single-file, browser-only tool for reconciling a working **Roster** against a **MasterList**
(the "originals" / source of truth), keyed on Badge Number. It flags every row as **Aligned**,
**Misaligned**, or **Badge Not Found in MasterList**, lets you fix mismatches one field, one row,
or all at once, and now checks new data entry against the originals **live, as you type** —
before you even save.

No build step, no backend, no dependencies. It's one `index.html` file that runs entirely in
the browser and autosaves your session locally as you work.

---

## Features

- **Spacing-tolerant comparison, exact-format correction** — stray or doubled spaces never
  cause a false mismatch, but any auto-corrected field is written back byte-for-byte identical
  to MasterList's own literal cell (spacing, casing, punctuation included).
- **Live Check (new)** — the Add/Edit form for a Roster record compares your in-progress entry
  against the matching MasterList record in real time as you type, showing Aligned /
  Misaligned / Not Found and a per-field diff *before* you save. The MasterList Add/Edit form
  live-checks the Badge Number for duplicates the same way.
- **Two-way CSV import (new)** — import CSV data into either the **Roster** or the
  **MasterList**. Re-importing a refreshed MasterList export updates existing reference
  records in place by Badge Number instead of duplicating them, so you can keep MasterList in
  sync with a periodically refreshed "original" source file.
- **Auto-Correct** — per field, per row, or for the entire Roster at once, all undo-safe.
- **Undo history** — step back through your last 25 auto-correct actions.
- **Promote** — turn a "Badge Not Found" Roster row into a new MasterList reference record.
- **Dashboard** — status counts, distribution bars, and top mismatched fields.
- **Filters, search, sorting, pagination** on both Roster and MasterList.
- **Audit log** — every correction, import, add, edit, delete, and settings change, with CSV
  export.
- **Configurable crosscheck** — turn individual fields on/off from the comparison.
- **CSV export** — full roster, errors-only, and audit log.
- **Local autosave (new)** — every change is saved to the browser's `localStorage` a moment
  after you make it. Reloading or reopening the tab restores your last session automatically.
  Nothing is sent to a server. Use **New Session** to deliberately wipe the saved data and
  start over from the bundled sample dataset.
- **11,000-record generated sample dataset** on first run, purely so every feature is
  demonstrable immediately.

---

## Getting started

### Option 1 — just open it
Download or clone the repo and open `index.html` directly in any modern browser
(Chrome, Edge, Firefox, Safari). That's it — there is nothing to install or build.

```bash
git clone https://github.com/<your-username>/<your-repo>.git
cd <your-repo>
open index.html      # macOS
# or: xdg-open index.html   (Linux)
# or: start index.html      (Windows)
```

### Option 2 — host it with GitHub Pages
1. Push this repo to GitHub.
2. In the repo, go to **Settings → Pages**.
3. Under **Build and deployment**, set **Source** to `Deploy from a branch`.
4. Choose the branch (e.g. `main`) and folder `/ (root)`, then **Save**.
5. GitHub will publish the site at `https://<your-username>.github.io/<your-repo>/`
   within a minute or two.

### Option 3 — any static host
Because it's a single static HTML file, it also works unmodified on Netlify, Vercel,
Cloudflare Pages, an S3 bucket with static hosting, an internal file share, etc. Just
upload `index.html`.

---

## Uploading this project to GitHub (step by step)

If you're starting from scratch:

1. **Create a new repository** on GitHub (don't initialize it with a README if you're
   pushing this one).
2. **Initialize git locally** in this folder and push:
   ```bash
   git init
   git add .
   git commit -m "Initial commit: Reconciliation Data Checker"
   git branch -M main
   git remote add origin https://github.com/<your-username>/<your-repo>.git
   git push -u origin main
   ```
3. **(Optional) Enable GitHub Pages** as described above to get a live URL you can share.
4. **Update this README's clone URL** above to match your actual repository.

---

## How it works

### Matching
Badge Number is the key field. It is looked up, never compared or auto-corrected. If a
Roster row's badge doesn't exist in MasterList, it's flagged **Badge Not Found** — add that
badge to MasterList, or fix a typo, to resolve it.

### Spacing-tolerant comparison, exact-format correction
Detecting a mismatch trims leading/trailing spaces and collapses doubled internal spaces
before comparing, so stray spacing alone never flags a false Misaligned row in either sheet.
But when a field **is** corrected — via a Fix button, Fix field, or Auto-Correct All — the
Roster receives MasterList's value exactly as typed there: same spacing, capitalization, and
punctuation, for every field, no exceptions. MasterList's own typing is always the standard.

### Live Check — validating data entry against the originals in real time
Open **+ Add Record** or **Edit** on a Roster row, and a **Live Check** panel appears below
the form. As you type (after a brief debounce), it looks up the Badge Number you've entered
against MasterList and shows:

- **Aligned** — every checked field already matches the original.
- **Misaligned** — the exact fields that differ, with a from → to diff, before you save.
- **Not Found** — no MasterList record uses that Badge Number yet.

On a MasterList Add/Edit form, the same panel instead flags a **duplicate Badge Number**
against the rest of MasterList, since MasterList must have exactly one record per badge.
Saving a duplicate badge is blocked with an explanatory message.

### Buttons & automation
| Action | What it does |
|---|---|
| **Auto-Correct All** | Fixes every Misaligned Roster row in one action, using MasterList's exact values. Undo-safe. |
| **Fix** / **Fix field** | Same correction, scoped to one row or one field. |
| **Undo** | Steps backward through your last 25 auto-correct actions, most recent first. |
| **Download Errors (CSV)** | Exports every Misaligned / Not Found row with its mismatch detail. |
| **Download Roster (CSV)** | Exports the full Roster with its live Verification Status. |
| **Import CSV** | Bulk-adds or updates rows in either the Roster or the MasterList from pasted or uploaded CSV text. Headers are matched by name. |
| **Settings** | Choose which fields are included in the crosscheck. |
| **Promote** | Inserts a "Badge Not Found" Roster row into MasterList as a new reference record. |
| **New Session** | Clears the autosaved browser data and reloads the sample dataset. |

### CSV import — header matching
Headers are matched case-insensitively and are flexible about punctuation, e.g. all of
`Badge No.`, `Badge Number`, `BadgeNo` map to the same field. Recognized headers:

| Field | Recognized header examples |
|---|---|
| Badge Number *(required)* | `Badge No.`, `Badge Number`, `BadgeNo` |
| Account Number | `Account Number`, `Salary Account Number` |
| Rank | `Rank` |
| Last Name | `Last Name` |
| First Name | `First Name` |
| Middle Name | `Middle Name` |
| Qualifier | `Qual`, `Qlfr`, `Qualifier` |
| Sub-Unit | `Sub Unit`, `Sub-Unit`, `Subunit` |

Importing into **Roster** appends new rows (each gets the next sequential Nr). Importing into
**MasterList** updates an existing reference record in place when its Badge Number already
exists, or adds a new one otherwise — so re-importing a refreshed original export keeps
MasterList in sync without creating duplicates.

### Autosave & data privacy
Every change (edits, imports, corrections, settings) is saved to this browser's
`localStorage` a moment after you make it; the indicator under the page title confirms when a
save completes. Reopening the tab restores your last session. Because the data lives only in
that browser profile, it will not follow you to another device or browser, and clearing
browser data removes it. Use **Download Roster (CSV)** / **Download Errors (CSV)** for a
durable, portable copy, and **New Session** to deliberately reset to the sample dataset.

Nothing in this tool makes network requests — it does not send your data anywhere.

### Settings — choosing which fields to check
Badge Number, Nr, and Verification Status are always system-managed and excluded from the
crosscheck. Every other field (Account Number, Rank, Last Name, First Name, Middle Name,
Qualifier, Sub-Unit) can be turned off from **Settings** if you don't want it checked yet.

---

## Bringing in your real data

1. Open **Import CSV**.
2. Choose **MasterList** as the target and import your reference/original export first.
3. Open **Import CSV** again, choose **Roster**, and import your working roster export.
4. Use **New Session** first if you want to discard the 11,000-record sample data entirely
   before importing your real data (otherwise your imports are simply added alongside it).

---

## Project structure

```
.
├── index.html      # the entire application (HTML + CSS + JS, no build step)
├── README.md        # this file
├── LICENSE           # MIT license
└── .gitignore
```

---

## Browser support

Any current version of Chrome, Edge, Firefox, or Safari. The app uses `localStorage`,
`Blob`/`URL.createObjectURL` for CSV downloads, and standard ES5-ish JavaScript — no bundler
or transpiler required.

## License

MIT — see [LICENSE](LICENSE).
