# 08 — Phase 0 Consolidated Report

**Project:** Monsun Labs Remake  
**Phase:** 0 — Discovery & Inventory  
**Date:** 2026-11-27  
**Overall verdict:** **PARTIAL — ACCESS LIMITATIONS**

Phase 0 discovery is substantially complete for all accessible Labs repositories and live HTTP surfaces. Residual gaps (org admin API, Pages UI settings, failed Actions logs, NetCDF internals, notebook runtime) prevent marking **COMPLETE**. Nothing is **BLOCKED**.

---

## 1. Repository inventory summary

| Repository | Owner | Local HEAD | Working tree | Role |
|------------|-------|------------|--------------|------|
| `monsun-labs.github.io` | `monsun-labs` | `main` `a731bb2` | Clean | Docs site (Zensical) |
| `extract-point` | `monsun-labs` (origin URL still `nusawave-labs`) | `add-extract-utils` `8f7b9d6` | Untracked `data/era5_sample.nc` | Pilot tutorial |
| `tyorus/extract-point` | fork | `fork/main` merged stripped WIP | — | Personal fork |
| `Monsun-Intelligence.github.io` | Monsun Intelligence | local `dev` | — | Hosts `monsun.io` incl. `/labs/` |

`monsun-labs/` folder is **not** a git root. Org listing shows only the two Labs repos above.

---

## 2. Findings by severity (rolled up)

Approximate unique findings across reports 01–06 (architecture report is design-only):

| Severity | Count (approx.) | Themes |
|----------|-----------------|--------|
| CRITICAL | 3 | HEAD hard-coded indices; empty `extract_utils`; missing Colab bootstrap |
| HIGH | ~18 | Branch/publish divergence; Nusawave on GitHub `main`; broken Monsun links; labs.monsun.io redirect failure; site↔WIP drift; media absence; no tutorial sync automation |
| MEDIUM | ~16 | Remotes, sample-spec drift, SEO typo, host branch ambiguity, secrets/dispatch naming, gitignore |
| LOW | ~10 | `nw-home__` prefix, favicon, roadmap staleness, polish |
| INFO | ~12 | Historical commits, topology notes, Zensical keep |

Exact IDs live in per-workstream reports; treat CRITICAL/HIGH as Phase 1 blockers for a coherent remake.

---

## 3. Most important Nusawave branding remnants

1. **Published** `extract-point` `@origin/main` still says Nusawave throughout README, LICENSE, notebook bootstrap URLs, plot titles, LinkedIn copy.
2. Local git remote `origin` host string `nusawave-labs` (resolves to `monsun-labs` via rename).
3. Website user-visible text is clean; residual **`nw-home__`** CSS prefix only.
4. Incomplete Monsun rebrand on WIP: broken `colab-link` and `monsunlab` GitHub slug.

---

## 4. Most important Extract Point findings

1. **Two incompatible trees:** full tutorial on GitHub `main` (Nusawave) vs stripped Monsun WIP on `add-extract-utils`/`fork/main`.
2. Website documents the **full** tutorial; WIP cannot satisfy those claims.
3. HEAD notebook contains **non-runnable** pedagogy on the 20×20 sample (`isel` 42/864) and missing multi-variable goals.
4. Release `v0.1-data` sample exists and matches intended Colab data strategy — wire-up broken on WIP.

---

## 5. Most important website inconsistencies

1. **`labs.monsun.io` does not redirect** — serves full duplicate site with canonical `monsun-labs.github.io` because redirect Actions run failed.
2. Canonical product URL `monsun.io/labs/` works and matches `zensical.toml`.
3. Meta description typo: **analystics**.
4. Colab CTA points at `main` → Nusawave-branded notebook content.
5. No custom logo/OG/tutorial media on the site.

---

## 6. Media & automation findings

**Media:** No first-party logo/OG/thumbs in website repo; scientific PNGs only on extract-point `main`; WIP deleted them; local untracked outputs present.

**Automation:** Site build exists; host `deploy-labs.yml` listens for `labs-updated`; Labs `docs.yml` redirect+dispatch path is **broken/unreliable**; tutorial repo has **no** real CI; no content/media sync pipeline.

---

## 7. Recommended publishing architecture

**Reusable workflow + manifest-driven PR into `monsun-labs.github.io` (Approach B), retaining existing `labs-updated` dispatch for host deploy after merge.**  
No notebook execution in CI. Human review required. Extract Point is the pilot after content stabilization.

Details: [`07-future-publishing-architecture.md`](07-future-publishing-architecture.md)

---

## 8. Proposed Phase 1 scope (authorization required — do not start yet)

1. **Stabilize Extract Point source of truth** — restore full tutorial capability from `origin/main`, apply Monsun brand/URLs, retire or rebase stripped WIP; fix indices, bootstrap, requirements-notebook, utils, benchmarks.
2. **Repair domain/CI** — fix `docs.yml` so `labs.monsun.io` redirects; confirm deploy token + host branch alignment; fix description typo; point README/homepage to `monsun.io/labs/`.
3. **Brand hygiene** — remotes → `monsun-labs`; LICENSE/README/notebook strings; leave history intact.
4. **Media baseline** — add favicon/logo/OG placeholders; decide which tutorial figures sync to the landing page.
5. **Publishing contract draft** — `labs-publish/manifest.yaml` schema only (implementation of reusable workflow can be Phase 2).
6. **Do not** replace Zensical; do not change canonical domain intent.

---

## 9. Access limitations (why PARTIAL)

| Limitation | Effect |
|------------|--------|
| `admin:org` missing | Cannot confirm org vs user metadata |
| Failed Actions log unavailable | Root cause of 0s failure unknown |
| Pages settings UI not read | Host branch (`main` vs `dev`) not fully confirmed |
| NetCDF not opened / notebook not run | Scientific claims partly static |
| `.venv` symlink Grep errors | Searches scoped to git paths |
| `monsun-labs.github.io` direct HTTP client failure | Inferred via `labs.monsun.io` content |

---

## 10. Report index

| # | Path |
|---|------|
| 01 | `audit/phase-0/01-workspace-and-repository-inventory.md` |
| 02 | `audit/phase-0/02-brand-audit.md` |
| 03 | `audit/phase-0/03-extract-point-technical-audit.md` |
| 04 | `audit/phase-0/04-website-and-content-audit.md` |
| 05 | `audit/phase-0/05-media-inventory.md` |
| 06 | `audit/phase-0/06-existing-automation-audit.md` |
| 07 | `audit/phase-0/07-future-publishing-architecture.md` |
| 08 | `audit/phase-0/08-phase-0-consolidated-report.md` |

Absolute root: `C:\Users\yothu\tyo\monsun.io\monsun-labs.github.io\audit\phase-0\`

---

## 11. Integrity statement

Phase 0 created **only** the eight Markdown files under `audit/phase-0/`.  

No existing project source files, Git branches, remotes, commits, PRs, Actions definitions, secrets, DNS, or production deployments were modified. Notebooks were not executed; ERA5 was not downloaded.

**Stop here.** Await explicit human review and authorization before Phase 1.
