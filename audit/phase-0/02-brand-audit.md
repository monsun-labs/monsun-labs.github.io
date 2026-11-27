# 02 — Brand Audit (Nusawave → Monsun)

**Phase:** 0 — Discovery & Inventory  
**Date:** 2026-11-27  
**Mode:** Read-only  
**Policy:** Do not auto-recommend rewriting historical commits or scientific/package identifiers.

---

## 1. Summary

| Surface | Nusawave remnants? | Monsun status |
|---------|--------------------|---------------|
| Website source (`docs/`, `zensical.toml`) | No visible “Nusawave” text | Monsun; CSS prefix `nw-home__` leftover |
| Website live `monsun.io/labs/` | Not observed | Monsun |
| `labs.monsun.io` | Not observed as Nusawave | Monsun content, **wrong canonical** (`monsun-labs.github.io`) |
| `extract-point` `origin/main` (published default) | **Heavy Nusawave** | Not rebranded |
| `extract-point` HEAD / `fork/main` | Mostly cleared | Monsun, but broken links / incomplete rebrand |
| Git remotes | `nusawave-labs` hostname on `origin` | Resolves to `monsun-labs` |
| Commit history | Rebrand commits exist | Historical — keep |

---

## 2. Finding catalog

### BRAND-01 — Published tutorial default still Nusawave

| Field | Value |
|-------|-------|
| Severity | **HIGH** |
| Component | `monsun-labs/extract-point` @ `origin/main` |
| Classification | Visible brand reference + external links |
| Evidence | `README.md`: “Part of Nusawave Labs”, Colab/GitHub URLs `github.com/nusawave-labs/extract-point`, `© Nusawave Labs 2026`; `LICENSE` copyright Nusawave; notebook bootstrap `REPO_URL` nusawave; LinkedIn “Follow Nusawave”; kernel `display_name: nusawave`; `extract_utils.py` header “Nusawave Labs” |
| Impact | Anyone opening Colab from website (`monsun-labs/.../main/...`) still lands on content that brands Nusawave (URLs may redirect, visible strings do not) |
| Remediation | Port Monsun strings onto the **canonical publish branch** without deleting scientific content; keep historical commits |
| Phase | 1 |
| Verification | `git grep -i nusawave origin/main` |

### BRAND-02 — Local `origin` remote still uses `nusawave-labs` host

| Field | Value |
|-------|-------|
| Severity | **MEDIUM** |
| Component | Local remotes |
| Classification | Repository ownership / remotes |
| Evidence | `git remote -v` → `origin git@github.com:nusawave-labs/extract-point.git`; `gh api repos/nusawave-labs/extract-point` → `full_name: monsun-labs/extract-point` |
| Impact | Developer confusion; scripts/docs may copy legacy host |
| Remediation | `git remote set-url origin git@github.com:monsun-labs/extract-point.git` (Phase 1 ops — not done in Phase 0) |
| Phase | 1 |
| Verification | `git remote -v` + `gh repo view` |

### BRAND-03 — Incomplete / incorrect Monsun links on HEAD

| Field | Value |
|-------|-------|
| Severity | **HIGH** |
| Component | HEAD `add-extract-utils` README + notebook |
| Classification | Visible brand reference / external link |
| Evidence | README Colab badge target = literal `colab-link`; notebook footer `[GitHub](github.com/monsunlab/extract-point)` (missing scheme, wrong slug `monsunlab`) |
| Impact | Broken user funnel despite Monsun naming |
| Remediation | Use `https://github.com/monsun-labs/extract-point` and full Colab URL |
| Phase | 1 |
| Verification | Open badge/link in browser |

### BRAND-04 — Website CSS/HTML class prefix `nw-home__`

| Field | Value |
|-------|-------|
| Severity | **LOW** |
| Component | `docs/index.md`, `docs/stylesheets/home.css` |
| Classification | Technical identifier |
| Evidence | Dozens of `.nw-home__*` selectors; no user-visible “Nusawave” string |
| Impact | Internal naming debt only; not user-facing brand |
| Remediation | Optional rename to `ml-home__` / `monsun-home__` during design pass; not urgent |
| Phase | 2+ |
| Verification | Grep `nw-home` |

### BRAND-05 — Website README live URL is non-canonical

| Field | Value |
|-------|-------|
| Severity | **MEDIUM** |
| Component | `monsun-labs.github.io/README.md` |
| Classification | External link / visible |
| Evidence | `**Live site:** [monsun-labs.github.io](https://monsun-labs.github.io)` while `zensical.toml` `site_url = https://monsun.io/labs/` |
| Impact | Contributors and SEO point at secondary host |
| Remediation | Point README to `https://monsun.io/labs/` |
| Phase | 1 |
| Verification | README + live HTTP |

### BRAND-06 — GitHub repo homepage metadata

| Field | Value |
|-------|-------|
| Severity | **LOW** |
| Component | GitHub repo settings (observed via API) |
| Classification | External link |
| Evidence | `homepageUrl: https://monsun-labs.github.io` |
| Impact | About box promotes non-canonical URL |
| Remediation | Set homepage to `https://monsun.io/labs/` |
| Phase | 1 |
| Verification | `gh api repos/monsun-labs/monsun-labs.github.io` |

### BRAND-07 — Historical rebrand commits

| Field | Value |
|-------|-------|
| Severity | **INFO** |
| Component | Git history |
| Classification | Historical reference |
| Evidence | Website `bfc451f Rebrand Nusawave Labs to Monsun Labs.`; extract-point `8f7b9d6 change nusawave to monsun` |
| Impact | None — expected archaeology |
| Remediation | **Do not rewrite history** for brand cleanup |
| Phase | — |
| Verification | `git log --grep=nusawave -i` |

### BRAND-08 — LinkedIn ID inconsistency

| Field | Value |
|-------|-------|
| Severity | **LOW** |
| Component | Notebook vs website |
| Classification | External link / ambiguous |
| Evidence | Website: `linkedin.com/company/monsunic`; HEAD notebook: `company/109905023` |
| Impact | Possible same entity (numeric vs slug); UX inconsistency |
| Remediation | Standardize on `monsunic` slug if it resolves |
| Phase | 1 |
| Verification | HTTP resolve both URLs |

### BRAND-09 — Deploy workflow legacy org string `monsunic`

| Field | Value |
|-------|-------|
| Severity | **INFO** |
| Component | `.github/workflows/docs.yml` |
| Classification | Technical identifier / remote |
| Evidence | Dispatch URL `repos/monsunic/monsunic.github.io/dispatches`; API resolves to `Monsun-Intelligence/...` |
| Impact | Rename alias may keep working; docs drift |
| Remediation | Update to current full name after confirming alias permanence |
| Phase | 1–2 |
| Verification | `gh api` |

### BRAND-10 — Website source has zero “Nusawave” strings

| Field | Value |
|-------|-------|
| Severity | **INFO** |
| Component | Website working tree |
| Classification | Observation |
| Evidence | `git grep -i nusawave HEAD` → empty (aside from historical commits) |
| Impact | Website text rebrand complete |
| Remediation | None |
| Phase | — |
| Verification | `git grep` |

### BRAND-11 — Benchmark/plot titles on `main` still Nusawave

| Field | Value |
|-------|-------|
| Severity | **MEDIUM** |
| Component | `origin/main` / local `main` benchmarks |
| Classification | Visible brand / asset text |
| Evidence | `benchmarks/benchmark.py` default title includes “Nusawave Labs”; `generate_assets.py` “Nusawave Labs”; notebook plot title string |
| Impact | Generated charts carry old brand if regenerated from `main` |
| Remediation | Replace when restoring/publishing Monsun tree |
| Phase | 1 |
| Verification | Grep + inspect PNG text if needed |

---

## 3. Classification counts

| Classification | Count (approx.) |
|----------------|-----------------|
| Visible brand reference | Many on `origin/main`; few broken on HEAD |
| Technical identifier | `nw-home__`, remote host, workflow repo names |
| Historical reference | Commit messages |
| External link | Colab, GitHub, LinkedIn, homepage |
| Asset / media | Benchmark titles; no Nusawave logo files found |
| Repository ownership/remotes | `nusawave-labs` → `monsun-labs` rename |
| Ambiguous | Numeric LinkedIn ID |

**No Nusawave logo SVG/PNG assets found** in either Labs repo tree.

---

## 4. What not to blindly replace

- Historical commit messages and past release notes.
- Rename redirects (`nusawave-labs` → `monsun-labs`) while GitHub still serves them — update *local remotes and docs*, do not assume old URLs are “wrong” for inbound links.
- Scientific dataset names, CDS product IDs, variable short names (`swh`, `tp`, etc.) — unrelated to brand.
- Kernel display names in old notebook outputs on `main` — clean when regenerating outputs.

---

## 5. Priority remediation order (Phase 1+)

1. Align **published** `extract-point` default branch content + brand with Monsun **without** losing tutorial completeness.
2. Fix HEAD broken Colab/GitHub links if that branch is retained.
3. Canonical URL consistency: README, GitHub homepage, `labs.monsun.io` redirect (see report 04).
4. Optional: rename `nw-home__` CSS prefix.
