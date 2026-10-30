# Receipt Numberer

<div align="center">

![Receipt Numberer](assets/icon.svg)

**Windows desktop app for numbering receipts in Ugandan computer centres**

[![Version](https://img.shields.io/badge/version-1.0.2-blue)](version.txt)
[![License](https://img.shields.io/badge/license-MIT-green)](#license)
[![Platform](https://img.shields.io/badge/platform-Windows-lightgrey)](#installation)

</div>

---

## What it does

In Ugandan computer centres, receipts are traditionally numbered with manual numbering machines. **Receipt Numberer** replaces that workflow — completely offline, with no internet required:

1. **Design** — Create your receipt layout in any tool (Word, Excel, Canva, etc.) and export it as a single-page PDF.
2. **Place** — Open the PDF in the app and click where each serial number should appear.
3. **Print** — Enter the start number and book size, then print N numbered sheets.
4. **Bind** — Cut the sheets and bind them into perforated receipt books.

The numbers are stamped directly onto the PDF, so what you see on screen is exactly what prints.

---

## Screenshots

| Design view — place slots on your PDF | Print run — scrub through sheets to verify numbers |
|:---:|:---:|
| ![Design](docs/screenshots/design.png) | ![Print run](docs/screenshots/print-run.png) |

| History — review past runs | Settings — theme & updates |
|:---:|:---:|
| ![History](docs/screenshots/history.png) | ![Settings](docs/screenshots/settings.png) |

---

## Installation

### Windows (recommended)

Download `ReceiptNumberer-Setup-1.0.2.exe` from the [Releases](https://github.com/Qaphael/receipt-numberer/releases) page, run it, and launch **Receipt Numberer** from the Start Menu or desktop.

App data is stored in `%APPDATA%\ReceiptNumberer\` and survives uninstall/upgrade.

### Portable

Download `ReceiptNumberer-1.0.2-portable.zip` from the same page, extract it anywhere, and run `ReceiptNumberer.exe` inside the extracted folder. No installation required.

### Automatic updates

The app checks the releases page once at launch (behind a 6-hour cooldown). When a newer version is published, it downloads and installs it by itself — the running app closes and the new one opens.

An update **never interrupts work**: it waits while the editor holds unsaved changes or a render/print job is running, and applies on the next free moment (or the next launch).

You can turn updates off, or check for them by hand, in **Settings → Updates**. The app reads the public releases page anonymously (no token ships in the app), and you can switch to strict offline mode with `RN_AUTO_UPDATE=0`.

Every release also ships `SHA256SUMS.txt` so you can verify a downloaded file by hand:
```cmd
certutil -hashfile ReceiptNumberer-Setup-1.0.2.exe SHA256
```

---

## How numbering works

The **Template** tab has a **Numbering mode** toggle. Both modes advance the counter by exactly the numbers the run consumes.

### Book stack (default — for perforated books)

Numbers flow down each book, then across. Slots that share a *book index* form one book (a body and its tear-off stub, for example) and print the **same number** on a sheet.

```
4 slots (each its own book), Book size = 50, Start = 1201

Sheet 1:   1201   1251   1301   1351
Sheet 2:   1202   1252   1302   1352
...
Sheet 50:  1250   1300   1350   1400

Next start: 1401
```

**Body + stub example** — two slots sharing book index 0 count as one book, so a B=50 run consumes 50 numbers and prints the same number in both positions.

Disabled slots consume no numbers; a book whose slots are all disabled consumes nothing, and the remaining active books renumber contiguously.

### Consecutive

Numbers run across the slots of each sheet, so every sheet gets the next sequence. Use this when each sheet is a set of separate receipts (e.g. two receipts per page, numbered 1/2, then 3/4).

```
2 slots, Book size = 50, Start = 1

Sheet 1:   1   2
Sheet 2:   3   4
...
Sheet 50:  99  100

Next start: 101
```

Slots are numbered in creation order, and disabled slots drop out of the sequence. Book number and tear direction are not used in consecutive mode.

---

## Using the app

The app has four views, switched from the header:

### Step 1 · Design

- **Load** your receipt PDF with the drop zone or file picker.
- **Place slots** — click to add one number per click (Shift-click keeps placing; press **N** twice to lock Add on).
- **Move** slots by dragging (one undo entry per gesture); nudge with arrow keys.
- **Rulers and guides** in millimetres — drag a guide off a ruler, double-click to type an exact value, drag it back or press **Delete** to remove it.
- **Snapping** to guides, page edges, centre, margins, other slots and the grid; **Alt/Ctrl** bypasses snapping for one drag.
- **Multi-select** with Shift/Ctrl-click, rubber-band, or **Ctrl+A**; move, nudge, rotate, align, distribute, delete or duplicate the whole selection.
- **Inspector** (right panel) sets X/Y in mm or pt, rotation (0/90/180/270°), alignment, direction, book number, label and enabled state. Mixed values show "−" and edits apply to the whole selection as one undo step.
- **Grid repeat** generates rows/columns from one slot with exact spacing, previewed as ghosts and undoable in one step.
- **Live validation** flags out-of-bounds, overlapping numbers and zero-pad overflow before you print.

### Step 2 · Print run

- **Sheet scrubber** — step through first / prev / slider / next / last ("Sheet N of B") and watch the numbers change on the sheet, so what you check is what prints.
- Pick the **template** and **number series**, set the **start number** (prefilled from the counter) and **book size**.
- A **live summary** shows the full run ("8 books × 50 leaves = 400 numbers, 1201–1600, next start 1601") plus per-book (or per-slot) ranges.
- **Enable/disable slots** per run for partial runs.
- **Test sheet** prints the numbers on sheet 1 without reserving anything.
- **Generate PDF** or **Print** (exact-size via SumatraPDF when present, otherwise the Windows print dialog).
- **Run lifecycle**: reserve → generate → print → confirm. Cancel a run — numbers go back if nothing was printed, otherwise they are marked spoiled. The **Current Run** panel offers confirm / cancel / reprint.

### Step 3 · History

- **Needs review** banner for runs still waiting on a decision, plus a per-series summary of the next free number.
- Filter by template, date range and search; sortable columns; pagination; CSV export.
- **Gap/overlap detection** across runs.

### Counter management

Numbers live in a **number series**, and several templates can share one series so one continuous sequence spans several receipt designs. A reserve-then-generate lifecycle means an interrupted run never hands the same number out twice.

Manual counter correction is available with a confirmation dialog and audit trail. Archive a template (kept, restorable) or delete it permanently (export its history to CSV first).

---

## Keyboard shortcuts

### Global (always active)

| Shortcut | Action |
|----------|--------|
| `Ctrl+Z` | Undo |
| `Ctrl+Y` / `Ctrl+Shift+Z` | Redo |
| `Ctrl+S` | Save template |
| `Ctrl+P` | Go to Print Run view |
| `Ctrl+0` | Fit page to window |
| `Ctrl+1` | Zoom to 100% |

### Design view only

| Shortcut | Action |
|----------|--------|
| `V` | Select tool |
| `N` | Add slot (double-tap to lock) |
| `H` | Hand tool (pan) |
| `Space` (hold) | Pan (drag) |
| `+` / `-` | Zoom in / out |
| `F` | Fit page |
| `Shift+2` | Zoom to selection |
| `R` | Rotate selected slot(s) |
| `D` | Duplicate selected slot(s) |
| `Delete` | Delete selected slot(s) / guide |
| `Ctrl+A` | Select all slots |
| Arrow keys | Nudge selection (Shift = 10×) |

> **Note:** Shortcuts are disabled while a dialog is open. `Space` only pans when the canvas has focus (click the page first).

---

## Look & feel

- **Light / dark / system themes**, switched from the header; applied before first paint so nothing flashes, and remembered across restarts.
- **Accessible by default**: one focus ring everywhere, ≥4.5:1 text contrast in both themes, and support for `prefers-reduced-motion` and `prefers-contrast`.
- The PDF page stays paper-white in both themes, and the printed number keeps its stamp red so the on-screen marker matches the ink.

---

## Where your data lives

Everything the app writes lives under `%APPDATA%\ReceiptNumberer\`:

| Folder / file | Purpose |
|---|---|
| `receipt_numberer.db` | Templates, number series, run history |
| `pdfs\` | Uploaded template PDFs |
| `outputs\` | Generated numbered PDFs |
| `tmp\` | Scratch files (swept at startup) |
| `logs\app.log` | Rotating log (5 × 1 MB) for unexpected errors |
| `settings.json` | UI theme and automatic-update switch |

It survives uninstall/upgrade, and the counter can be edited manually.

---

## System requirements

- Windows 10 or later
- The **WebView2 runtime** (installed automatically by the installer; the portable build needs it present on the machine)
- No internet connection required for normal use (only for optional automatic updates)

---

## Troubleshooting

| Problem | Solution |
|---------|----------|
| App won't start / "did not start within 15 seconds" | Check `%APPDATA%\ReceiptNumberer\logs\app.log` for details. Ensure WebView2 runtime is installed. |
| Numbers look wrong on the printed sheet | Use the **Sheet scrubber** in Print Run to verify every sheet before printing. Check the numbering mode (Book stack vs Consecutive). |
| Printer not listed / print fails | The app uses SumatraPDF for exact-size printing. If missing, it falls back to the Windows print dialog (scaling may not be exact). Install SumatraPDF or check printer name in Settings. |
| "Another instance is already running" | Only one copy of the app can run at a time. Check the system tray or Task Manager. |
| PDF upload fails | Must be a valid PDF, ≤ 25 MB, at least 1 page. Only page 1 is used. |
| Update check fails | The app checks anonymously. If offline, it retries on next launch. You can disable auto-updates in Settings. |

---

## Frequently asked questions

**Q: Can I use this on Linux or Mac?**  
A: The packaged app is Windows-only (WebView2, PyInstaller, Inno Setup). The source code can run the backend + frontend in a browser on any platform for development.

**Q: Can I use my own fonts?**  
A: The app bundles a set of licensed fonts. Select one in the Template tab → Typography. System fonts are not used, so every machine prints identically.

**Q: How do I back up my data?**  
A: Copy the entire `%APPDATA%\ReceiptNumberer\` folder. The database and PDFs are all there.

**Q: Can I share templates between computers?**  
A: Export the template's PDF from the `pdfs\` folder and the database row, or recreate the slots on the other machine (slots are stored in the DB).

**Q: What happens if the power goes out during printing?**  
A: The run lifecycle is reserve → generate → print → confirm. If power fails before *confirm*, the run stays in *generated* or *sent* state. On next launch, the "Needs review" banner lets you confirm or void it. Numbers are never reused.

**Q: How do I number two receipts per page (top/bottom)?**  
A: Place two slots, set both to the same **book index** (they become one book), choose **Book stack** mode, and set **Book size** to the number of sheets per book.

---

## Privacy & offline-first

- **No telemetry, no analytics, no network calls** during normal operation.
- The only network request is the optional automatic update check (once per launch, 6-hour cooldown, anonymous, disableable with `RN_AUTO_UPDATE=0`).
- All data stays on your machine.

---

## License

MIT. See the **Third-party notices** link in the app (or `THIRD_PARTY_NOTICES.md`) for the bundled components, including SumatraPDF (GPLv3, launched as a separate process for exact-size printing).

---

## Support

- **Issues**: [GitHub Issues](https://github.com/Qaphael/receipt-numberer/issues)
- **Releases**: [GitHub Releases](https://github.com/Qaphael/receipt-numberer/releases)

---

Built for Ugandan computer centres to replace manual numbering machines. Designed for offline-first, rugged environments.