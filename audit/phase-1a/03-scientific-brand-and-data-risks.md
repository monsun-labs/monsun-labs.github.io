# 03 — Scientific, Brand, and Data Risks

**Phase:** 1A — Source of Truth Decision  
**Date:** 2026-11-27  
**Mode:** Static analysis only (no NetCDF open, no notebook/script execution)

---

## 1. Scientific & reproducibility risks

### 1.1 Hard-coded indices

| Ref | Evidence | Class |
|-----|----------|-------|
| `add-extract-utils` / `72755ec` | Notebook contains `latitude=42` (and companion global-style indexing) | **STATICALLY VERIFIED** presence of unsafe pattern |
| `origin/main` / local `main` | Uses `lat_idx` / `lon_idx` from `argmin`; `benchmarks/benchmark.py` defines `nearest_indices` | **STATICALLY VERIFIED** safer pattern exists |

**Impact scoped to WIP lineage.** Not a defect of `origin/main` content.

| ID | Severity | Finding |
|----|----------|---------|
| SR-01 | CRITICAL (WIP experience) | Magic `isel` indices incompatible with documented 20×20 sample — **RUNTIME UNVERIFIED** IndexError, but statically inconsistent with `data/README` grid |

### 1.2 Coordinate / lon conventions

| Topic | Evidence | Class |
|-------|----------|-------|
| Dim name candidates | `extract_utils.detect_dims` on om: latitude/lat/y, longitude/lon/x | DOCUMENTATION + code static |
| Lon wrap −180/180 vs 0–360 | No normalizer found in utils/notebook | **STATICALLY VERIFIED absence**; risk **DOCUMENTATION-ONLY** for Natuna 108°E |
| Lat ordering | Not validated against file | **DATA-METADATA UNVERIFIED** |

### 1.3 Variables, units, renames

| Topic | Evidence | Class |
|-------|----------|-------|
| CDS mapping in `download_era5.py` | `significant_height_of_combined_wind_waves_and_swell`→`swh`, `peak_wave_period`→`tp`, `u10`/`v10` | STATIC in scripts |
| `DEFAULT_RENAME` on om utils | `swh`→`hs_m`, `tp`→`tp_s`, wind/swell aliases | STATIC |
| Actual NetCDF var set in Release asset | Not opened | **DATA-METADATA UNVERIFIED** |
| Units in file attrs | Not read | **DATA-METADATA UNVERIFIED** |
| Notebook multi-var path on om | `from extract_utils import extract_point, add_wind_speed, quick_stats` | STATIC connection verified |
| WIP notebook | `swh` focus only; goals text may overclaim | STATIC |

### 1.4 Grid resolution & sample period

| Claim | Where | Class |
|-------|-------|-------|
| `0.25°` in WIP notebook | `add-extract-utils` probe `res_0_25=True` | STATIC inconsistency vs scripts `GRID_SPACING_DEG = 0.5` |
| om notebook avoids `0.25` teaching claim | probe False | Better aligned with wave 0.5° docs |
| om `data/README`: 2024, 1 year, ~120 MB, Release | STATIC | |
| WIP `data/README`: 2020–2024, 5 years, ~270 MB, “committed to repo” | STATIC | Conflicts with Release reality |
| `DEFAULT_YEARS` | om=`1`, WIP=`5` | STATIC |

### 1.5 Paths & Dask demos

| Ref | Issue | Class |
|-----|-------|-------|
| WIP | `era5_natuna_10yr.nc` referenced | STATIC missing-file risk |
| om | Uses `DATA_PATH` | STATIC |
| Lazy/eager teaching | Present both lineages with different quality | STATIC pedagogy; **RUNTIME UNVERIFIED** |

### 1.6 Dependencies

| Ref | Learner file | Maintainer file |
|-----|--------------|-----------------|
| om / local main | `requirements-notebook.txt` present | `requirements.txt` (cartopy/cdsapi) |
| WIP | missing notebook requirements | older `requirements.txt` blob |

### 1.7 Must validate in Phase 1B (explicit)

1. Open Release/`local` `era5_sample.nc` **read-only** for dims, coord monotonicity, variable names, units, time span, approx size consistency with docs.
2. Dry-run import of `extract_utils` against that file (still optional later) — if authorized.
3. Confirm Colab URL with `monsun-labs` path resolves after content publish.
4. Confirm website landing claims vs chosen baseline tip.
5. Lon-wrap decision for tutorial scope (document limitation vs implement).

**Do not claim scientific correctness or runtime success from Phase 1A.**

---

## 2. Brand, attribution, licensing

### 2.1 Inventory (selected)

| Occurrence | Refs | Classification |
|------------|------|----------------|
| `Copyright (c) 2026 Nusawave Labs` in LICENSE | om, local main, `72755ec` | **COPYRIGHT HOLDER** (active on published tip) |
| `Copyright (c) 2026 Monsun Labs` in LICENSE | WIP / fork | **COPYRIGHT HOLDER** change attempt — **do not auto-merge** |
| README / © Nusawave | om | **ACTIVE BRAND DEBT** |
| README / © Monsun + `monsun-labs.github.io` | WIP | **ACTIVE BRAND** intent + non-canonical site URL |
| Colab URL `nusawave-labs/...` | om README | **ACTIVE BRAND DEBT** / may redirect |
| `](colab-link)` | WIP README | **BROKEN LINK** |
| Notebook Nusawave strings / kernel name | om | **ACTIVE BRAND DEBT** |
| Notebook Monsun plot title | WIP `8f7b9d6` | Brand intent OK |
| `github.com/monsunlab/extract-point` | WIP | **BROKEN LINK** |
| `github.com/nusawavelab/extract-point` | pre-brand stub | **BROKEN LINK** (historical on stub) |
| Benchmark default titles “Nusawave Labs” | om `benchmark.py` / `generate_assets.py` | **ACTIVE BRAND DEBT** |
| Utils header “Nusawave Labs” | om | **ACTIVE BRAND DEBT** / **LEGACY TECHNICAL NAME** |
| LinkedIn numeric `109905023` | both (label Nusawave→Monsun on WIP) | **UNKNOWN** vs site slug `monsunic` |
| Commit messages mentioning nusawave | history | **HISTORICAL REFERENCE** — preserve |
| Remote URL host `nusawave-labs` | local git config | **LEGACY TECHNICAL NAME** |
| ERA5 / CDS / ECMWF | scripts & docs | **THIRD-PARTY ATTRIBUTION** — keep |
| MIT license text body | all | Legal form — keep |

### 2.2 Critical licensing caution

**SR-BR-01 (HIGH):** Replacing “Nusawave Labs” with “Monsun Labs” in `LICENSE` copyright **changes the stated copyright holder**. Phase 1A does **not** establish corporate succession, assignment, or rights.  

**Recommended action:** Human/legal confirmation before editing LICENSE. Until then, treat om LICENSE as authoritative published text. Brand user-facing docs can move to Monsun **without** silently rewriting copyright if ownership is unsettled.

**Do not rewrite Git history** to erase Nusawave commits.

### 2.3 Proposed branding change inventory (not implemented)

| Target (from om baseline) | Proposed intent | Caution |
|---------------------------|-----------------|---------|
| README Part-of / © / clone URLs | Monsun Labs + `https://monsun.io/labs/` + `monsun-labs/extract-point` | OK after product decision |
| Colab badge | `github/monsun-labs/extract-point/blob/main/...` | After main content publish |
| Notebook bootstrap `REPO_URL` / Release URLs | `monsun-labs` | |
| Utils/benchmark plot titles | Monsun | |
| LICENSE copyright line | **Pending ownership confirmation** | |
| Historical commits | Leave | |
| CDS/ECMWF attribution | Strengthen if missing | Third-party |

---

## 3. Data & media strategy

### 3.1 Release evidence

| Item | Evidence |
|------|----------|
| Tag | `v0.1-data` |
| Asset | `era5_sample.nc` |
| GitHub size | 118,466,500 bytes (Phase 0 API) |
| GitHub digest | `sha256:f11e80d5e7a7769ff26f19ebf2f842839afa3a801f750c32cbbb341b52e1aab4` |
| Local untracked file size | 118,466,500 bytes |
| Local SHA256 (Get-FileHash) | `F11E80D5E7A7769FF26F19EBF2F842839AFA3A801F750C32CBBB341B52E1AAB4` |

**Finding SR-DA-01 (INFO):** Local untracked `data/era5_sample.nc` **matches** the Release asset digest and size → treat as the same bytes for provisional use. Still **do not modify/delete** it in 1A/without authorization.

### 3.2 Provisional sample distribution approach

**Use GitHub Release `v0.1-data` / `era5_sample.nc` as the provisional tutorial data source**, as documented on `origin/main` (wget/Colab bootstrap).  

Reject WIP “commit NetCDF into git” narrative for the ~118 MB teaching file.

**Checksum requirement for Phase 1B+:** verify SHA-256 equals `f11e80d5e7a7769ff26f19ebf2f842839afa3a801f750c32cbbb341b52e1aab4` after any download.

### 3.3 `.gitignore`

| Ref | NetCDF rules |
|-----|----------------|
| om / local main | Ignores `data/era5_sample.nc` (+ benchmark large nc); un-ignores `data/benchmark/*` and `download_area.png` |
| WIP | No NetCDF exceptions — higher accidental-commit risk |

### 3.4 Essential tutorial assets

| Asset | Keep in tutorial repo | Publish to website |
|-------|----------------------|--------------------|
| Release NetCDF | Via Release only | No |
| `data/download_area.png` | Yes (om) | Optional sync later |
| `data/benchmark/era5_large_benchmark.{png,csv}` | Yes (om) | Optional |
| Notebook-embedded outputs | Prefer regenerate; avoid huge IPYNB bloat long-term | No raw embeds to site |
| Logo/OG | N/A here | Website phase |

---

## 4. Findings summary

| ID | Severity | Component | Evidence | Impact | Action | Verification | Phase |
|----|----------|-----------|----------|--------|--------|--------------|-------|
| SR-01 | CRITICAL | WIP notebook | `latitude=42` | Broken on 20×20 sample | Do not ship WIP notebook | static + later runtime | 1B |
| SR-02 | HIGH | WIP science copy | `0.25°`, 10yr file name | Mis-teaches ERA5 wave grid | Use om notebook | grep | 1B |
| SR-03 | HIGH | Sample docs WIP | 5yr/270MB/commit | Conflicts Release | Prefer om docs | README diff | 1B |
| SR-BR-01 | HIGH | LICENSE | Copyright string diverge | Legal risk if wrong | Human ownership confirm | counsel | 1B |
| SR-BR-02 | HIGH | Links WIP | `colab-link`, `monsunlab` | Broken UX | Fix on rebrand pass | HTTP | 1B |
| SR-BR-03 | MEDIUM | om brand | Nusawave URLs/titles | Brand debt on published tip | Rebrand after baseline lock | grep | 1B |
| SR-DA-01 | INFO | Local nc | SHA256 match Release | Safe provisional local sample | Keep; don't commit | hash | 1A |
| SR-DA-02 | MEDIUM | WIP gitignore | No nc ignore | Accidental large commit | Restore om gitignore | diff | 1B |
| SR-04 | MEDIUM | Lon wrap | No helper | Edge-case reuse risk | Document in 1B | code review | 1B |
| SR-05 | INFO | NetCDF attrs | Not opened | Unknown vars/units in file | Phase 1B metadata pass | ncdump/xarray read-only | 1B |
