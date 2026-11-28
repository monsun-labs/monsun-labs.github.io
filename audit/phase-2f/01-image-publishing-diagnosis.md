# Phase 2F — Image Publishing Diagnosis

**Date:** 2026-11-28  
**Mode:** Diagnose → remediate via new PRs (no history rewrite)

## Root cause (multi-factor)

### R1 — Host deploy never ran after website PR #5 (blocks live visibility)

**Class:** Deployment / secret gap (not converter extraction)

**Evidence:**

- Actions run `46112531273` (merge PR #5) succeeded locally for Zensical + redirect, but log shows:
  `Skipping: MONSUNIC_DEPLOY_TOKEN not set`
- Host `Monsun-Intelligence/Monsun-Intelligence.github.io` `main` tip still `46bb9e6` (2026-11-24)
- Host path `labs/tutorials/extract-point/` contains only `index.html` (no `media/`, no `body/`)
- Live `https://monsun.io/labs/tutorials/extract-point/` still shows pre-PR#5 science (`20 × 20`, no Auto-converted link)
- Live `/labs/tutorials/extract-point/body/` falls through to the main Monsun site shell (page absent on host)

### R2 — Primary tutorial page has no figure embeds

**Class:** Website publishing / content contract (`replace_index: false`)

**Evidence:**

- Curated [`docs/tutorials/extract-point/index.md`](../../docs/tutorials/extract-point/index.md) describes plots in prose only
- Only link: “Auto-converted notebook Markdown … [body.md](body.md)”
- Live index CDP: sole `<img>` is the Colab badge

### R3 — Notebook lacks committed image outputs for most plots

**Class:** Missing notebook outputs (for file-based displays)

**Evidence** (`extract_point.ipynb`):

| Cell | Mechanism | Committed image output? |
|------|-----------|-------------------------|
| 26 | `plt.show()` → `display_data` `image/png` (~420k chars) | **Yes** → extracted as `media/output_26_0.png` |
| 28 | `Image(filename="benchmark_sample.png")` | **No** (runtime-only PNG; not in git) |
| 30 | `Image(filename=str(png))` → `data/benchmark/era5_large_benchmark.png` | **No** display_data |

### R4 — Converter copies allowlisted media but does not emit Markdown refs

**Class:** Markdown reference generation failure

**Evidence:**

- `body.md` contains exactly one image ref: `![png](media/output_26_0.png)`
- `media/era5_large_benchmark.png` and `media/download_area.png` exist on disk but are **unreferenced**
- Local Zensical build rewrites body image to `src="../media/output_26_0.png"` (path OK once page is deployed)

## Not root causes

- nbconvert extraction of committed `display_data` PNG — works for cell 26
- Zensical relative path rewrite for `body/` sibling pages — correct locally
- Media copy into website tree — files present on `main` after PR #5

## Remediation (smallest reliable fix)

1. **Policy A (extract-point):** enhance converter to resolve `Image(filename=…)` / allowlisted assets into `media/` + Markdown refs without executing; validate every local image ref resolves; fail on dangling refs.
2. **Policy B (website):** regenerate `body.md`; embed publishable figures into curated `index.md` so the canonical tutorial URL shows images.
3. **Ops (no config edit):** trigger existing host `deploy-labs` workflow (or restore `MONSUNIC_DEPLOY_TOKEN`) so `monsun.io/labs/` rebuilds.

## Live verification (partial)

After manually triggering host `deploy-labs` (run `46114712751`):

- Landing page reflects PR #5 copy (`39 × 39`, Auto-converted link) but still **only** the Colab badge image (R2/R4 remain).
- `https://monsun.io/labs/tutorials/extract-point/body/` renders `media/output_26_0.png` at 1548×1187 (committed display_data extraction + Zensical path OK).

Full landing-page figure success requires merging website PR #6 and redeploying.

## Success criteria

Live `https://monsun.io/labs/tutorials/extract-point/` renders notebook-derived figures (not only the Colab badge), with resolved `media/*` URLs.
