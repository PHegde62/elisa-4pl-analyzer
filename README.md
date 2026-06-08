# ELISA 4PL Analyzer

A single-file, zero-install web tool that turns a plate-reader workbook into an
analyzed summary — fitting the standard curve with a **4-parameter logistic
(4PL) model and 1/Y² weighting**, equivalent to GraphPad Prism's
"Sigmoidal, 4PL, X is concentration" fit.

Everything runs **in your browser**. No data is uploaded anywhere, no install,
works offline.

## Live tool

If published with GitHub Pages (see below), open:

```
https://<your-username>.github.io/elisa-4pl-analyzer/
```

Or just download `index.html` and double-click it.

## How to use

1. Open the page.
2. Drag in an `.xlsx` plate workbook (or click to browse).
3. Set the **sample dilution factor** and **reporting unit** (µg/mL, ng/mL, pg/mL).
4. Review the fit (EC50, Hill slope, R²), the standard-curve plot, and the
   Standards / QC / Samples tables.
5. Click **Download** to save your original workbook with a clean **Summary**
   tab added (Raw Data and Plate Map are preserved).

### Supported input layouts (auto-detected)

The tool detects which layout each workbook uses:

**1. Raw Data + Plate Map grid**

- **Raw Data** — an 8×12 plate grid of OD values (rows A–H, columns 1–12).
  `overflow` / saturated cells are recognized and excluded.
- **Plate Map** — the matching 8×12 grid of well labels (e.g. `Standard 1`,
  `HQC`, `Blank`, `Unknown 12`), plus a small lookup table with columns
  `Sample` and `Conc (pg/mL)` giving the nominal concentrations. Replicates are
  grouped by adjacency, so repeated labels in separate blocks (e.g. an HQC
  bracket at each end of the plate) stay distinct.

**2. Pre-labeled section tables** (one or more plate/dilution sheets per file)

Sheets containing labeled tables — `… Standard`, `… QC`, `Samples`, and/or
inhibitor dose-response blocks — where each row already lists its concentration
(or sample/inhibitor value) and OD replicates (2 or 3). No plate map needed.
Per-sheet dilution is read from the sheet name (e.g. `1to100`, `1:10`) when
present, otherwise from the dilution box. Starred outliers (`0.735*`) and
`overflow` cells are excluded.

Both PK concentration assays and **dose-response inhibition assays** are
supported. For dose-response plates, the tool fits a 4PL per compound and
reports **IC50** (`>max dose` when there is little/no inhibition over the tested
range).

Each analyzed sheet gets its own Summary tab appended to your original
workbook; all original sheets are preserved.

## What it reports

- 4PL fit parameters: Bottom, Top, EC50, Hill slope, R², LLOQ/ULOQ.
- Per sample/standard/QC: mean OD, %CV, back-calculated concentration,
  accuracy (for standards/QCs) or converted concentration (for samples).
- Automatic QC flags: overflow wells, OD above the curve plateau, values
  beyond LLOQ/ULOQ (extrapolated), and replicate %CV > 20%.

The downloaded Summary uses **live Excel formulas** (with cached values), so it
recalculates in Excel if you edit an OD.

## Method / validation

The fit uses the increasing 4PL form on a linear concentration axis with
frozen-weight iteratively-reweighted least squares — Prism's relative (1/Y²)
weighting scheme:

```
Y = Bottom + (Top − Bottom) / (1 + (EC50 / X)^HillSlope)
```

Validated against a Prism-analyzed plate: fitted parameters matched to within
~0.2% (EC50 555.2 vs 554.9, Hill 1.492 vs 1.492) and all back-calculated
concentrations agreed within 0.15%.

## Publish it on GitHub Pages (≈2 minutes)

1. Create a new repository named `elisa-4pl-analyzer` on GitHub.
2. Upload these files (`index.html`, `README.md`, `LICENSE`) — drag-and-drop on
   the repo page, then **Commit changes**.
3. Go to **Settings → Pages**.
4. Under **Build and deployment**, set **Source: Deploy from a branch**, branch
   `main`, folder `/ (root)`, then **Save**.
5. Wait ~1 minute; your link appears at the top of the Pages settings:
   `https://<your-username>.github.io/elisa-4pl-analyzer/`

Share that link — anyone can use the tool, and their data never leaves their
browser.

### Or via the command line

```bash
git init
git add index.html README.md LICENSE
git commit -m "ELISA 4PL Analyzer"
git branch -M main
git remote add origin https://github.com/<your-username>/elisa-4pl-analyzer.git
git push -u origin main
# then enable Pages in Settings → Pages as above
```

## Privacy

All parsing, curve fitting, and file generation happen locally in the browser
using a bundled copy of [SheetJS](https://sheetjs.com). Nothing is sent to any
server.

## License

MIT — see [LICENSE](LICENSE).
