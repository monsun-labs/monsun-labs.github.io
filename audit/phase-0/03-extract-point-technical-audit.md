# 03 — Extract Point Technical Audit

**Phase:** 0 — Discovery & Inventory  
**Date:** 2026-11-27  
**Mode:** Read-only (no notebook execution, no ERA5 download, NetCDF not opened)  
**Local path:** `C:\Users\yothu\tyo\monsun.io\monsun-labs\extract-point`

---

## 1. Branch matrix (verified)

| Artifact | HEAD `add-extract-utils` `8f7b9d6` | Local `main` `7006552` | `origin/main` `3416d57` | `fork/main` `0e383d4` |
|----------|-----------------------------------|------------------------|-------------------------|------------------------|
| Notebook cells | ~29, stripped | ~36, full | Full (−small vs local main) | Same as HEAD |
| `src/extract_utils.py` | **0 bytes** | ~139 lines | Same as local main | **0 bytes** |
| `benchmarks/benchmark.py` | **0 bytes** | ~210 lines | Present | **0 bytes** |
| `benchmarks/generate_assets.py` | Missing | Present | Present | Missing |
| `requirements-notebook.txt` | Missing | Present | Present | Missing |
| `data/benchmark/*` | Missing | Present | Present | Missing |
| `data/download_area.png` | Missing | Present | Present | Missing |
| Brand | Monsun (incomplete links) | Nusawave | Nusawave | Monsun (incomplete) |
| Colab badge | `colab-link` stub | Real nusawave URL | Real nusawave URL | Stub |
| `scripts/download_era5.py` defaults | `DEFAULT_YEARS=5` | `DEFAULT_YEARS=1` (local main) | 1-year sample story | Follows HEAD lineage |

**Interpretation:** GitHub’s default `main` still has the complete tutorial (Nusawave-branded). The Monsun rename branch emptied/removed the educational infrastructure. `fork/main` merged that stripped tree.

Website landing page documents the **full** tutorial (aligned with `origin/main` capability, Monsun URLs).

---

## 2. Scientific audit

### Verified defects

| ID | Severity | Finding | Evidence | Impact | Remediation | Phase | Verification |
|----|----------|---------|----------|--------|-------------|-------|--------------|
| EP-S1 | **CRITICAL** | Hard-coded global `isel` indices on HEAD | `ts_isel = ds["swh"].isel(latitude=42, longitude=864)` and naive `[:, 42, 864]` | Out of range on documented 20×20 sample → runtime failure | Restore derived `lat_idx`/`lon_idx` from `argmin` as on `main`/website | 1 | Static review; optional later runtime |
| EP-S2 | **HIGH** | Grid resolution misstated on HEAD | Notebook comment `0.25° × 0.25°`; scripts `GRID_SPACING_DEG = 0.5`; website correctly 0.5° wave | Teaches wrong ERA5 wave grid | Fix copy to 0.5° (~55 km) | 1 | Diff vs CDS docs / scripts |
| EP-S3 | **HIGH** | Stated goals Hs/Tp/wind; code extracts `swh` only | Intro text vs §3–§4 only `ds["swh"]` | Educational false advertising | Restore multi-var `extract_utils` path | 1 | Cell inventory |
| EP-S4 | **HIGH** | Dask demo opens missing file | `xr.open_dataset("era5_natuna_10yr.nc", ...)` | Cell fails | Point at `DATA_PATH` / sample | 1 | Path exists check |
| EP-S5 | **MEDIUM** | Time span inconsistency | Intro “10 years”; HEAD `data/README` “2020–2024 (5 years)”; website/Release “~120 MB / 2024 / 1 year” | Confused expectations | Single sample spec across README/site/scripts/Release | 1 | Cross-doc table |
| EP-S6 | **MEDIUM** | Sample size comment wrong | Notebook “~few MB” vs ~118–270 MB narratives | Misleading | Align with Release asset size (~118 MB uploaded) | 1 | Release asset metadata |

### Concerns (not executed)

| ID | Severity | Finding | Evidence |
|----|----------|---------|----------|
| EP-S7 | MEDIUM | No lon wrap 0–360 vs −180–180 helper | Absent in notebook and `main` utils |
| EP-S8 | LOW | Conceptual global 721×1440 diagram vs regional sample | Website labels this better than HEAD |
| EP-S9 | INFO | `chunks=None` “eager” pedagogy | Acceptable teaching shorthand on `main` |

**ERA5 product (from scripts, not runtime):** `reanalysis-era5-single-levels` via CDS; variables mapped to `swh`, `tp`, `u10`, `v10`. Additional wave vars (`mwd`, `shts`, …) appear in `main` `DEFAULT_RENAME` / website table — **not verified inside NetCDF** this phase.

**Units (documented, not measured):** Hs/`swh` meters; `tp` seconds; wind m/s — consistent with ERA5 conventions in docs/scripts.

---

## 3. Technical quality

| ID | Severity | Finding | Evidence | Remediation | Phase |
|----|----------|---------|----------|-------------|-------|
| EP-T1 | **CRITICAL** | Empty `extract_utils.py` on HEAD/fork | `git cat-file -s` → 0 | Restore from `origin/main`, rebrand header | 1 |
| EP-T2 | **CRITICAL** | No Colab/Step 0 bootstrap on HEAD | Missing clone/pip/wget cells present on `main` | Restore bootstrap + Monsun URLs | 1 |
| EP-T3 | **HIGH** | Inconsistent data paths | `../data/era5_sample.nc` vs bare `era5_sample.nc` | Single `DATA_PATH` | 1 |
| EP-T4 | **HIGH** | Missing `requirements-notebook.txt` | Absent on HEAD; present on `origin/main` | Restore light deps file | 1 |
| EP-T5 | **HIGH** | Hollow benchmarks | Empty `benchmark.py`; no RAM tracking | Restore `main` benchmark module | 1 |
| EP-T6 | **MEDIUM** | Weaker error handling vs `main` | `main` checks LFS pointer / min bytes | Port checks | 1 |
| EP-T7 | **MEDIUM** | Download default years drift | HEAD `DEFAULT_YEARS=5` vs website 1-year sample | Align defaults to published Release | 1 |
| EP-T8 | LOW | Full `requirements.txt` includes cartopy/cdsapi | Fine for maintainers; heavy for learners without split file | Keep split on publish branch | 1 |
| EP-T9 | INFO | `main` utils quality | Dim auto-detect, wind math, optional Dask — solid base | Rebrand + lon helper later | 1–2 |

**Reproducibility:** Release `v0.1-data` asset `era5_sample.nc` exists (size 118,466,500 bytes, sha256 recorded by GitHub). Local untracked copy present. HEAD does not wire Colab to download it.

**Colab compatibility:** Broken on HEAD (no bootstrap, stub badge). Website Colab badge points at `monsun-labs/.../main/...` → **Nusawave-branded full notebook** on current GitHub default — runnable shape likely closer to `origin/main`, brand wrong.

---

## 4. Educational quality

| ID | Severity | Finding |
|----|----------|---------|
| EP-E1 | HIGH | Magic indices instead of derived nearest-neighbor pedagogy |
| EP-E2 | HIGH | Post-processing / multi-var / wind direction story missing |
| EP-E3 | MEDIUM | Time-only micro-benchmark vs promised time+RAM + large-file section |
| EP-E4 | MEDIUM | Site statistics (`quick_stats`) missing |
| EP-E5 | LOW | Draft comment “show this as markdown ASCII art” |
| EP-E6 | LOW | Closing copy “three methods” vs four methods shown |

**Prerequisites:** Website says “None — first tutorial”; HEAD README similar. Acceptable if bootstrap works.

**References:** ERA5/CDS mentioned; formal citation block incomplete on HEAD vs fuller site page.

---

## 5. Repository quality

| ID | Severity | Finding | Evidence |
|----|----------|---------|----------|
| EP-R1 | HIGH | Non-functional Colab badge | `](colab-link)` |
| EP-R2 | HIGH | Broken GitHub markdown link | `github.com/monsunlab/extract-point` |
| EP-R3 | MEDIUM | Brand fragmentation across refs | See report 02 |
| EP-R4 | MEDIUM | `.gitignore` NetCDF rules dropped on HEAD | Risk of accidental huge commits |
| EP-R5 | LOW | Empty `.github/colab-badge.yml` | Stub since `origin/main` too (0-byte blob `e69de29`) |
| EP-R6 | INFO | MIT LICENSE present | Copyright Monsun on HEAD; Nusawave on `origin/main` |
| EP-R7 | INFO | No automated tests found | Neither branch has `tests/` |
| EP-R8 | MEDIUM | Contribution guidance thin | README quick-start only |

**Sample data strategy:** Correct pattern on `origin/main`/website = GitHub Release, not git LFS. HEAD `data/README` still says “committed to the repo” and 5-year/~270 MB — inconsistent with Release reality.

---

## 6. Website ↔ repo consistency matrix

| Website claim | `origin/main` | HEAD / fork/main |
|---------------|---------------|------------------|
| Step 0 bootstrap | Yes | **No** |
| `requirements-notebook.txt` | Yes | **No** |
| `extract_utils` API | Yes (Nusawave header) | **Empty** |
| Multi-var + wind | Yes | **No** |
| Time + RAM benchmarks | Yes | Time-only / empty module |
| `data/benchmark` assets | Yes | **Missing** |
| Sample ~120 MB / 1 year / 0.5° | Largely yes | Divergent README/scripts |
| Colab → `monsun-labs` / `main` | Badge uses Monsun path; **content Nusawave** | N/A (stub) |
| Derived lat/lon indices | Yes | **Hard-coded 42/864** |

---

## 7. `main` utils surface (for restoration baseline)

From `git show main:src/extract_utils.py` (read-only):

- `DEFAULT_RENAME` for `swh`, `tp`, `mwd`, swell vars, `u10`, `v10`
- Dim candidate tuples for lat/lon/time
- `find_data_file`, `extract_point`, `add_wind_speed`, `quick_stats`
- xarray lazy open + select + DataFrame export

**Not verified:** behavior against actual NetCDF variable inventory in Release asset.

---

## 8. Access limitations

- Notebooks not executed; IndexError on 42/864 is **static** reasoning from documented 20×20 grid.
- NetCDF variables/attrs/units inside `era5_sample.nc` not inspected.
- Grep across `.venv` failed (Windows symlink os error 1920) — searches limited to git-tracked paths.

---

## 9. Recommended Phase 1 technical direction (no implementation)

Treat **`origin/main` capability** as the scientific/educational baseline; apply **Monsun brand + URL fixes**; discard or rebase the stripped `add-extract-utils` content unless intentionally designing a new shorter tutorial (would then require website rewrite).
