# 02 — Capability and File Diff Matrix

**Phase:** 1A — Source of Truth Decision  
**Date:** 2026-11-27  
**Mode:** Read-only  
**Refs compared:** `origin/main` (`3416d57`), local `main` (`7006552`), `add-extract-utils` (`8f7b9d6`), `fork/main` (`0e383d4`), merge-base `72755ec`

---

## 1. Blob identity matrix (selected artifacts)

Legend: OID short + size. `ABSENT` = path missing. size `0` = empty blob `e69de29…`.

| Path | `72755ec` | `origin/main` | local `main` | `add-extract-utils` / `fork/main` |
|------|-----------|---------------|--------------|-------------------------------------|
| `src/extract_utils.py` | empty 0 | `4011386` 4705 | same as om | empty 0 |
| `benchmarks/benchmark.py` | empty 0 | `d2bc215` 6559 | same | empty 0 |
| `benchmarks/generate_assets.py` | ABSENT | `c6ec5af` 3301 | same | ABSENT |
| `requirements-notebook.txt` | ABSENT | `8b79bfe` 189 | same | ABSENT |
| `requirements.txt` | `b1eac90` 136 | `b6c9db3` 150 | same | `b1eac90` 136 |
| `README.md` | `7de3eb6` 778 | `85e27a3` 1732 | same | `f7af353` 772 (Monsun) |
| `LICENSE` | Nusawave `cdff98d` | same | same | Monsun `ce87008` |
| `.gitignore` | thin `a344f27` 80 | rich `af1a227` 356 | same | thin `a344f27` |
| `data/README.md` | 5yr/`dfec498` | 1yr Release/`0dd4c43` | same | 5yr/`dfec498` |
| `scripts/download_era5.py` | `4d19761` YEARS=5 | `651c2f2` YEARS=1 | same | `4d19761` YEARS=5 |
| `scripts/plot_download_area.py` | `0def06d` | same | same | same |
| `notebooks/extract_point.ipynb` | `4877232` 16724 | `f6c6817` 673790 | `424e8f5` 674366 | `809a198` 16718 |
| `data/benchmark/*` | ABSENT | present | present | ABSENT |
| `data/download_area.png` | ABSENT | present | present | ABSENT |
| `.github/colab-badge.yml` | empty 0 | empty 0 | empty 0 | empty 0 |

**Classification of `fork/main` vs WIP:** **IDENTICAL** tree (no file diffs).

**Classification of local `main` vs `origin/main`:** notebook **PATH same / BRAND-ONLY? No** → **FUNCTIONAL?** No → **metadata-only EDUCATIONAL-neutral** cell `id` additions (`7006552`).

---

## 2. Per-artifact classification (`origin/main` → WIP)

| Artifact | Class | Notes |
|----------|-------|-------|
| `src/extract_utils.py` | **EMPTIED** (relative to om) / stub on WIP lineage | Never populated on WIP; full on om via `905b1ba` |
| `benchmarks/benchmark.py` | **EMPTIED** (rel. om) | Same |
| `benchmarks/generate_assets.py` | **DELETED** (rel. om) / never on WIP | Added only on main line |
| `requirements-notebook.txt` | **DELETED** (rel. om) | Main-line add |
| `data/benchmark/*`, `download_area.png` | **DELETED** (rel. om) | Main-line add |
| `notebooks/extract_point.ipynb` | **EDUCATIONAL + FUNCTIONAL** rewrite lineage | 36 vs 29 cells; om full vs WIP stub+brand |
| `README.md` | **BRAND-ONLY** on WIP vs its parent; vs om also **FUNCTIONAL/EDU** (missing Release/Colab instructions) | WIP README still thin |
| `LICENSE` | **BRAND-ONLY** (copyright holder string) | Legal review required before adopting Monsun line |
| `.gitignore` | **FUNCTIONAL** (rel. om richer) | WIP lacks NetCDF/Release ignore exceptions |
| `data/README.md` | **FUNCTIONAL + EDUCATIONAL** | 5yr/270MB/committed vs 1yr/120MB/Release |
| `scripts/download_era5.py` | **FUNCTIONAL** | `DEFAULT_YEARS` 5 vs 1; other drift in `905b1ba` |
| `scripts/plot_download_area.py` | **IDENTICAL** | Same blob all tips |
| `.github/colab-badge.yml` | **IDENTICAL** empty | Not a real workflow |

Empty-file verdict: empty utils/benchmark on WIP are **long-standing placeholders from merge-base**, not an accidental wipe inside `8f7b9d6`. Relative to the **published full tutorial**, they are capability gaps.

---

## 3. Notebook cell probe (static JSON parse; no execution)

| Signal | `origin/main` | local `main` | `add-extract-utils` | `72755ec` |
|--------|---------------|--------------|---------------------|-----------|
| Cell count | 36 (23 md / 13 code) | 36 | 29 (20 md / 9 code) | 29 |
| Step 0 / `REPO_URL` / requirements-notebook | Yes | Yes | No | No |
| `lat_idx` nearest selection | Yes | Yes | No | No |
| Hard-coded `latitude=42` | No | No | **Yes** | **Yes** |
| `extract_utils` import | Yes | Yes | No | No |
| `add_wind_speed` / `quick_stats` | Yes | Yes | No | No |
| `run_benchmark` | Yes | Yes | No | No |
| `era5_natuna_10yr.nc` | No | No | **Yes** | **Yes** |
| `0.25°` resolution claim | No | No | **Yes** | **Yes** |
| Nusawave strings | Yes | Yes | No | Yes |
| Monsun strings | No | No | Yes | No |
| `monsunlab` broken slug | No | No | **Yes** | No (had `nusawavelab`) |
| `DATA_PATH` | Yes | Yes | No | No |

`tracemalloc` lives in `benchmarks/benchmark.py` on om (not inlined in notebook source) — RAM capability present via module.

---

## 4. Capability preservation matrix

Presence requires **populated implementation connected to the learner path**, not merely an empty path.

| Capability | `origin/main` | local `main` | `add-extract-utils` | `fork/main` |
|------------|---------------|--------------|---------------------|-------------|
| Colab bootstrap | **Yes** (Step 0 + README URL) | **Yes** | **No** (no Step 0; README `colab-link` stub) | **No** |
| Sample download (Release wget) | **Yes** | **Yes** | **No** | **No** |
| Nearest grid-point selection | **Yes** (`lat_idx`/`lon_idx` + `nearest_indices` in bench) | **Yes** | **No** (magic 42/864) | **No** |
| Multi-variable extraction | **Yes** (`extract_point` + rename map) | **Yes** | **No** (`swh`-centric stub notebook) | **No** |
| Hs / `swh` | **Yes** | **Yes** | **Partial** (code uses `swh`; indices unsafe) | **Partial** |
| Tp / `tp` | **Yes** (via utils/pipeline; outputs show `tp_s`) | **Yes** | **No** (not extracted in stub flow) | **No** |
| Wind speed and direction | **Yes** (`add_wind_speed`) | **Yes** | **No** | **No** |
| Dask demonstration | **Yes** (on `DATA_PATH`) | **Yes** | **Partial/broken** (`era5_natuna_10yr.nc`) | **Partial/broken** |
| Time benchmark | **Yes** (`run_benchmark`) | **Yes** | **Partial** (inline time-only plot) | **Partial** |
| RAM benchmark | **Yes** (`tracemalloc` in `benchmark.py`) | **Yes** | **No** (empty module) | **No** |
| Quick statistics | **Yes** (`quick_stats`) | **Yes** | **No** | **No** |
| Error handling | **Stronger** (LFS/min-size patterns on om) | **Same** | **Weaker** | **Weaker** |
| Notebook requirements file | **Yes** | **Yes** | **No** | **No** |
| Scientific plots (committed) | **Yes** (benchmark PNG, download_area) | **Yes** | **No** | **No** |
| Data sample strategy | **Release ~120 MB / 1 yr** | **Same** | **Docs say commit / 5 yr / ~270 MB** | **Same as WIP** |

---

## 5. Capabilities lost if WIP selected as baseline

All rows marked Yes only on om/`main` above would be lost or degraded: bootstrap, Release download path, nearest-index pedagogy, multi-var/wind/stats, RAM benchmarks, committed scientific figures, learner requirements split, Release-aligned sample docs.

---

## 6. Valuable deltas unique to WIP (for preservation inventory)

From `8f7b9d6` only:

1. `LICENSE` copyright line → `Monsun Labs` (**legal review**, not auto-apply).
2. README “Part of Monsun Labs” + © Monsun (**fix URLs**; currently points at `monsun-labs.github.io` and `colab-link`).
3. Notebook plot title / LinkedIn label “Monsun” (**keep intent**; LinkedIn still numeric ID).
4. GitHub star link retarget attempt (**do not keep** `monsunlab` slug; broken).

No unique scientific/algorithmic improvements on WIP versus om.

---

## 7. Findings

| ID | Severity | Component | Evidence | Impact | Action | Verification | Phase |
|----|----------|-----------|----------|--------|--------|--------------|-------|
| FD-01 | CRITICAL | Capability | Empty utils/bench on WIP; full on om | Tutorial non-equivalent | Baseline = full main-line tree | blob sizes + notebook probes | 1B |
| FD-02 | HIGH | Notebook | Magic indices + missing file on WIP | Static failure risk | Discard WIP notebook body | cell probes | 1B |
| FD-03 | HIGH | README WIP | `colab-link` stub | Broken funnel | Rebuild README from om + Monsun URLs | open badge | 1B |
| FD-04 | MEDIUM | Sample docs | 5yr vs 1yr / Release | Reproducibility confusion | Keep om data strategy | data/README diff | 1B |
| FD-05 | LOW | local main | Cell IDs only | Negligible | Optional keep via basing on local main | `git show 7006552` | 1B |
| FD-06 | INFO | fork/main | Identical to WIP | No extra files | Ignore as separate content source | `git diff` | 1A |
