# 05 — Media Inventory

**Phase:** 0 — Discovery & Inventory  
**Date:** 2026-11-27  
**Mode:** Read-only (no convert/move/delete)

---

## 1. Summary

| Location | Image/media assets | Branding |
|----------|--------------------|----------|
| Website source `docs/` | **No** committed PNG/SVG/JPG/ICO | N/A |
| Website build `site/assets/images/favicon.png` | Theme default favicon (1870 bytes) | Neutral / theme |
| Extract Point HEAD | No tracked plot PNGs; local untracked notebook outputs | Monsun text in notebook |
| Extract Point `origin/main` | `data/download_area.png`, `data/benchmark/*` | Titles may say Nusawave when regenerated from scripts |
| External | Colab badge SVG from Google | Neutral |
| Social OG | **Missing** | — |

Monsun Labs currently has **no first-party logo, hero photograph, or Open Graph image** in the Labs website repository.

---

## 2. Asset register

### Website

| Asset | Location | Type | Purpose | Referenced by | Branding | Attribution | Status |
|-------|----------|------|---------|---------------|----------|-------------|--------|
| `favicon.png` | `site/assets/images/favicon.png` (build) | PNG | Browser tab icon | Built HTML `rel=icon` | Theme default | Theme/Zensical | Current default; not custom Monsun |
| Inline SVG wave | `docs/index.md` hero | SVG path | Decorative wave | Home only | Neutral | N/A (inline) | Current |
| Colab badge | External URL | SVG | CTA | Extract Point page | Google Colab | External | Current |
| Custom logo | — | — | Header brand mark | `zensical.toml` logo commented | — | — | **Missing** |
| OG / social image | — | — | Link previews | — | — | — | **Missing** |
| Tutorial thumbnail | — | — | Tutorials index / cards | Home cards are CSS-only | — | — | **Missing** |
| Scientific diagrams on site | — | — | Explain NetCDF / workflow | Extract Point page uses ASCII/code only | — | — | **Missing** (text substitutes) |

### Extract Point — tracked on `origin/main`

| Asset | Location | Type | Size (bytes) | Purpose | Referenced by | Branding | Attribution | Status |
|-------|----------|------|--------------|---------|---------------|----------|-------------|--------|
| `download_area.png` | `data/download_area.png` | PNG | 166186 | CDS download domain map | Maintainer script `plot_download_area.py`; notebook/site may reference | Neutral map (titles via script) | ERA5/CDS data underlying | Present on `main`; **absent on HEAD** |
| `era5_large_benchmark.png` | `data/benchmark/` | PNG | 81686 | Large-file benchmark chart | Notebook §5.2 on `main`; website describes | May embed “Nusawave” if generated from old script titles | Generated | Present on `main`; **absent on HEAD** |
| `era5_large_benchmark.csv` | `data/benchmark/` | CSV | 197 | Benchmark numbers | Companion to PNG | Neutral | Generated | Present on `main` |

### Extract Point — local untracked (working tree)

| Asset | Location | Type | Size | Purpose | Status |
|-------|----------|------|------|---------|--------|
| `era5_sample.nc` | `data/` | NetCDF | ~118 MB | Sample dataset | Untracked; matches Release size |
| `benchmark_sample.png` | `notebooks/` | PNG | 80980 | Local run output | Untracked / unclear vs HEAD notebook |
| `timeseries_quicklook.png` | `notebooks/` | PNG | 412534 | Local plot output | Untracked |
| `benchmark_sample.csv` | `notebooks/` | CSV | 195 | Local bench | Untracked |
| `natuna_timeseries.csv` | `notebooks/` | CSV | ~1.1 MB | Export sample | Untracked |

### Extract Point — HEAD tracked

No PNG/JPG/SVG in `git ls-tree HEAD` for media. Notebook may still embed or save figures at runtime (not executed this phase).

---

## 3. Reference analysis

| Reference | Target | Broken? |
|-----------|--------|---------|
| Website Extract Point page | External Colab badge SVG | No |
| Website Extract Point page | Local tutorial diagrams | Uses ASCII — no broken img tags found |
| `zensical.toml` favicon | `images/favicon.png` under docs | Commented — unused path |
| `main` notebook §5.2 | `data/benchmark/era5_large_benchmark.png` | Valid on `main` |
| HEAD notebook | Does not reference missing benchmark assets | N/A |
| Site home cards | No `<img>` thumbnails | Design choice / gap |

**Finding MED-01 (HIGH):** Website has zero custom brand media (logo, OG, tutorial thumbs). LinkedIn → site funnel lacks share imagery.

**Finding MED-02 (HIGH):** Scientific/visual assets that support the published tutorial story live only on extract-point `main`, not synced into website repo — and are deleted on Monsun WIP branch.

**Finding MED-03 (MEDIUM):** No media manifest or sync path; future publishing cannot version captions/alt/attribution.

**Finding MED-04 (MEDIUM):** Local untracked notebook PNGs/CSVs risk accidental commit; unclear provenance vs Release sample.

**Finding MED-05 (LOW):** Benchmark PNG titles may still say Nusawave if regenerated from `main` scripts (see brand audit).

**Finding MED-06 (INFO):** Inline SVG wave is fine decorative media; not a product photograph/anchor.

**Finding MED-07 (INFO):** Sample NetCDF hosted on GitHub Releases (`v0.1-data`) — correct pattern; not duplicated in website repo.

---

## 4. Duplicates / external hosts

| Item | Notes |
|------|-------|
| Duplicate site hosts | Same built pages on `monsun.io/labs` and `labs.monsun.io` (media identical theme favicon) |
| External badge | `colab.research.google.com/assets/colab-badge.svg` |
| Release binary | `era5_sample.nc` on GitHub Releases only (intended) |

---

## 5. Copyright / attribution

| Asset class | Status |
|-------------|--------|
| ERA5-derived plots | Need CDS/ECMWF attribution in captions when published on site |
| Theme favicon | Zensical/Material default — replace before strong brand claims |
| Generated benchmarks | Attribution unclear in PNG itself |
| Website content license | CC-BY 4.0 (`LICENSE-CONTENT`) — applies to docs text; media policy not spelled out per asset |

---

## 6. Recommendations for later phases (no action now)

1. Add Monsun logo + favicon + OG image under `docs/assets/` (or agreed media dir).
2. Sync selected tutorial figures (download area, benchmark, quicklook) into website landing via manifest.
3. Require alt text + attribution fields in media manifest.
4. Keep large NetCDF on Releases only.
5. Do not commit local untracked notebook outputs without review.
