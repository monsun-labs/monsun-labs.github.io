# 01 — Workspace & Repository Inventory

**Phase:** 0 — Discovery & Inventory  
**Date:** 2026-11-27  
**Mode:** Read-only  
**Workspace root inspected:** `C:\Users\yothu\tyo\monsun.io\`

---

## 1. Executive inventory

| Path | Git root? | Remote owner | Purpose | Status |
|------|-----------|--------------|---------|--------|
| `monsun-labs.github.io/` | Yes | `monsun-labs` | Monsun Labs public docs site (Zensical) | Active |
| `monsun-labs/` | **No** | — | Organizational folder only | Active (container) |
| `monsun-labs/extract-point/` | Yes | remotes resolve to `monsun-labs` (+ fork `tyorus`) | Pilot tutorial | Active / WIP branch divergence |
| `monsun.github.io/` | Yes | `Monsun-Intelligence` | Corporate site `monsun.io` (hosts `/labs/`) | Active (related deploy target) |
| `downscale/` | Yes | `Monsun-Intelligence` | Separate product | Out of Labs scope |
| `monsun-cms/` | Yes | `Monsun-Intelligence` | CMS | Out of Labs scope |
| `monsun-forecast/` | Yes | `monsunic` | Forecast product | Out of Labs scope |

**Finding INV-01 (INFO):** Local folder boundaries ≠ Git repository boundaries. `monsun-labs/` is not a git repo; only nested `extract-point/` is.

---

## 2. Repository detail — `monsun-labs.github.io`

| Field | Value |
|-------|-------|
| Repository | `monsun-labs.github.io` |
| Owner | `monsun-labs` (GitHub user/org login; org admin API returned 404 for this token — treat as Labs hub account) |
| Remote | `git@github.com:monsun-labs/monsun-labs.github.io.git` (fetch/push) |
| Local branch | `main` @ `a731bb2d2c2aabf50766320fb44cf64def1ad9fe` |
| Tracking | `origin/main` — clean, up to date |
| Remote branches | `origin/main`, `origin/gh-pages` (`b7b77f9…`) |
| Latest commit | 2026-08-22 — `add redirection` |
| Working tree | Clean |
| Purpose | Landing pages, tutorial index, open-data reference, community |
| Homepage metadata | GitHub `homepageUrl` = `https://monsun-labs.github.io` (not canonical `monsun.io/labs/`) |
| Status | Active; last Actions redirect deploy **failed** (see report 06) |

### Key paths

- `docs/` — Markdown source
- `zensical.toml` — site config (`site_url = https://monsun.io/labs/`)
- `site/` — checked-in build output
- `redirect/` — intended `labs.monsun.io` → `monsun.io/labs/` stubs
- `.github/workflows/docs.yml` — build + gh-pages redirect + dispatch

---

## 3. Repository detail — `extract-point`

| Field | Value |
|-------|-------|
| Repository | `extract-point` |
| Owner (canonical) | `monsun-labs` |
| Local path | `C:\Users\yothu\tyo\monsun.io\monsun-labs\extract-point\` |
| Remote `origin` URL | `git@github.com:nusawave-labs/extract-point.git` |
| Remote `origin` resolution | GitHub API resolves to `monsun-labs/extract-point` (**org/user rename**) |
| Remote `fork` | `git@github.com:tyorus/extract-point.git` (fork; parent = `monsun-labs/extract-point`) |
| Current branch | `add-extract-utils` @ `8f7b9d691c338a346df986fff7d3080a583c1d6c` |
| Tracking | `fork/add-extract-utils` (up to date) |
| Local branches | `add-extract-utils`, `main` (`7006552`), `dev` (`ad63ac9`) |
| Remote branches | `origin/main`, `origin/dev`, `origin/add-extract-utils`, `fork/main`, `fork/add-extract-utils` |
| Working tree | Dirty — untracked `data/era5_sample.nc` (~118 MB) |
| Purpose | ERA5 point-extraction tutorial (notebook + scripts + sample data release) |
| Status | **Diverged / WIP** — see branch matrix |

### Branch tip SHAs (verified)

| Ref | SHA | Date | Subject |
|-----|-----|------|---------|
| HEAD `add-extract-utils` | `8f7b9d6` | 2026-08-19 | change nusawave to monsun |
| Local `main` | `7006552` | 2026-08-19 | minor updates |
| `origin/main` (**GitHub default**) | `3416d57` | 2026-06-21 | Merge branch dev into main |
| `fork/main` | `0e383d4` | 2026-08-19 | Merge PR #2 from tyorus/add-extract-utils |
| Tag `v0.1-data` | release asset `era5_sample.nc` | 2026-06-21 | Tutorial sample data (~118 MB) |

**Finding INV-02 (HIGH):** Published GitHub default (`origin/main`) is still June 2026 Nusawave-branded full tutorial. Local/fork Monsun rebrand lives on `add-extract-utils` / `fork/main` and is a **stripped** tree (empty utils/benchmarks, no `requirements-notebook.txt`). Website docs describe the **full** tutorial shape.

**Finding INV-03 (MEDIUM):** Local clone primary push remote for current branch is `fork` (`tyorus`), not `monsun-labs`. `origin` still uses legacy `nusawave-labs` hostname (works via rename redirect).

---

## 4. Related deploy host — `monsun.github.io` / Monsun Intelligence

| Field | Value |
|-------|-------|
| Local path | `C:\Users\yothu\tyo\monsun.io\monsun.github.io\` |
| Remote | `Monsun-Intelligence/Monsun-Intelligence.github.io` |
| Default branch (API) | `dev` |
| Homepage | `https://monsun.io` |
| Labs role | Hosts canonical `/labs/` via workflows that build Zensical from `monsun-labs/monsun-labs.github.io` and copy into `labs/` |

**Finding INV-04 (INFO):** Workflow in Labs repo still posts `repository_dispatch` to `monsunic/monsunic.github.io`. API resolves that name to `Monsun-Intelligence/Monsun-Intelligence.github.io` (rename). Behavior should still work if rename aliases remain, but string is legacy.

**Finding INV-05 (MEDIUM):** `deploy-labs.yml` checks out site `ref: main` while repo default branch is `dev`. Publish step pushes `HEAD:main`. Dual-branch hosting risk — verify which branch GitHub Pages / custom domain actually serves (**PARTIAL** without Pages settings access).

---

## 5. Org / account inventory (accessible)

`gh repo list monsun-labs` returned:

1. `monsun-labs/monsun-labs.github.io`
2. `monsun-labs/extract-point`

No other Labs tutorial repos found under this login.

`gh api orgs/monsun-labs` → **404 / needs `admin:org` scope** — cannot confirm whether `monsun-labs` is a user or org from this token.

Sibling Monsun product remotes under the same parent folder (not Labs tutorials): `Monsun-Intelligence/*`, `monsunic/monsun-forecast`.

---

## 6. Nusawave association

| Evidence | Classification |
|----------|----------------|
| Local `origin` URL host `nusawave-labs` | Legacy remote string; resolves to `monsun-labs` |
| Commit `bfc451f` on website: “Rebrand Nusawave Labs to Monsun Labs” | Historical |
| `origin/main` extract-point content still says Nusawave | Active published brand debt |
| No separate live `nusawave-labs/extract-point` distinct from Monsun | Rename, not parallel fork of upstream |

---

## 7. Access limitations

| Area | Limitation |
|------|------------|
| Org settings / members | `admin:org` not granted |
| GitHub Pages UI settings | Not queried (would not modify; metadata incomplete) |
| Secret values | Intentionally not read |
| NetCDF internals | Not opened (Phase 0 rule) |
| Failed Actions log `32562149955` | Log not found (0s run, empty jobs) |

---

## 8. Findings summary (this report)

| ID | Severity | Component | Evidence | Impact | Remediation | Phase | Verification |
|----|----------|-----------|----------|--------|-------------|-------|--------------|
| INV-01 | INFO | Workspace | `monsun-labs/` has no `.git` | Confusion about “repo” | Document org folder vs tutorial repos | 1 | `git rev-parse` |
| INV-02 | HIGH | extract-point | `origin/main` ≠ `fork/main` content/brand | Colab/`main` links hit Nusawave or outdated tree | Reconcile branches; publish one source of truth | 1 | Compare SHAs + trees |
| INV-03 | MEDIUM | Remotes | `origin` = nusawave host; push via `fork` | Wrong-remote push risk | Retarget `origin` to `monsun-labs`; clarify fork workflow | 1 | `git remote -v` |
| INV-04 | INFO | Deploy | `monsunic/...` → Monsun-Intelligence rename | Docs/automation string drift | Update workflow strings after confirm | 1–2 | `gh api` |
| INV-05 | MEDIUM | Host site | `deploy-labs` uses `main` vs default `dev` | Deploy may miss live branch | Align checkout/push with Pages branch | 1 | Pages settings + workflow |
| INV-06 | HIGH | extract-point | Untracked local `era5_sample.nc`; HEAD missing notebook requirements/utils | Local ≠ documented publish path | Standardize Release-based data; restore publish tree | 1 | Tree + Release asset |
