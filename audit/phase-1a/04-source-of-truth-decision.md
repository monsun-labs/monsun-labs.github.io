# 04 — Source of Truth Decision

**Phase:** 1A — Source of Truth Decision  
**Date:** 2026-11-27  
**Verdict:** **COMPLETE** (decision evidence sufficient from local objects; scientific runtime still deferred to 1B)

---

## 1. Executive summary

Extract Point has **two lineages** that diverged at `72755ec`:

- **Main line** (`origin/main` → local `main`): received `905b1ba` “huge updates” and is the **only** lineage with a complete, connected tutorial (bootstrap, utils, benchmarks, Release-aligned data docs, nearest-index pedagogy).
- **WIP line** (`add-extract-utils` → `fork/main`): **did not** receive that work; `8f7b9d6` only applies a Monsun string rename on the **thin stub**, plus broken Colab/GitHub links.

Therefore the provisional **content/capability source of truth** is the **main-line full tutorial**, tip preferred **local `main` @ `7006552`** (superset of `origin/main` with notebook cell IDs). Monsun branding from WIP is an **inventory of intended string changes**, not a viable code baseline.

**Recommended integration strategy: A** — restore/publish from the full main-line tree, then rebrand carefully (with LICENSE copyright gated on ownership confirmation).

---

## 2. Verified repository topology

| Ref | Role |
|-----|------|
| `origin/main` `3416d57` | Published GitHub default; full Nusawave-branded tutorial |
| Local `main` `7006552` | Descendant of `origin/main`; +notebook cell `id` metadata |
| `add-extract-utils` `8f7b9d6` | Thin stub + Monsun rename; current local HEAD |
| `fork/main` `0e383d4` | Merge of WIP; **tree identical** to WIP |
| Merge-base with main line | `72755ec` (pre-full tutorial) |

Remotes unchanged: `origin`→`nusawave-labs/...` (resolves to monsun-labs), `fork`→`tyorus/...`.  
Working tree dirty only with untracked `data/era5_sample.nc` (SHA256 matches Release).

---

## 3. Branch ancestry findings

- WIP is **not** a stripped fork of the full tutorial; it is a **parallel rename of the pre-`huge-updates` tree**.
- Unique WIP commit: `8f7b9d6` (3 files, brand strings).
- Unique local-main commit: `7006552` (cell IDs).
- Main-line unique work at risk if WIP chosen: utils, benchmarks, assets, notebook rewrite, requirements-notebook, Release docs, gitignore, download defaults.

Details: [`01-git-ancestry-and-branch-comparison.md`](01-git-ancestry-and-branch-comparison.md)

---

## 4. Capability preservation matrix (condensed)

| Capability | om / local main | WIP / fork/main |
|------------|-----------------|-----------------|
| Colab bootstrap + Release download | Yes | No |
| Nearest grid selection | Yes | No (magic 42/864) |
| Multi-var Hs/Tp/wind + stats | Yes | No |
| Time + RAM benchmarks + figures | Yes | No / hollow |
| Learner requirements file | Yes | No |
| Release-aligned sample strategy | Yes | No (5yr/commit narrative) |

Full matrix: [`02-capability-and-file-diff-matrix.md`](02-capability-and-file-diff-matrix.md)

---

## 5. Valuable WIP changes to preserve

Preserve **as a change list**, not as a merge of the WIP tree:

1. User-facing “Monsun Labs” naming intent (README Part-of, © line in docs, notebook plot title, LinkedIn **display** name).
2. Awareness that LICENSE was edited to Monsun — **requires ownership confirmation** before applying.
3. Nothing else: no unique science, no unique bootstrap, no unique tests.

**Do not preserve:** `colab-link` stub, `monsunlab` GitHub slug, thin notebook body, empty utils/benchmarks, 5-year/270 MB commit-oriented data docs, `DEFAULT_YEARS=5` as the published default.

---

## 6. Risks and unresolved questions

| Risk | Status |
|------|--------|
| WIP indices / missing Dask file | Statically verified unsafe on WIP |
| NetCDF variables/units/time in Release file | **Unresolved** — Phase 1B |
| Runtime of om notebook / Colab | **Unresolved** — Phase 1B |
| Copyright holder Nusawave vs Monsun | **Unresolved** — human/legal |
| Lon-wrap edge cases | Document; optional later code |
| Whether `7006552` cell IDs must ship | Low importance |
| Remote URL rename ops | Deferred; not content SoT |

---

## 7. Recommended baseline

| Decision | Choice |
|----------|--------|
| **Provisional content baseline** | **Local `main` @ `7006552`** |
| **Why not only `origin/main`?** | Local main = om + harmless notebook cell IDs; still full tutorial |
| **Why not WIP?** | Missing essentially all post-`72755ec` educational implementation |
| **Published remote tip today** | `origin/main` remains what GitHub `main` serves until a future authorized publish |
| **Brand reference inventory** | Diff `8f7b9d6` + Monsun product URLs from Labs site (`monsun.io/labs/`, `monsun-labs/*`) |

---

## 8. Recommended integration strategy

### Strategy A — Restore full tutorial from main line, then rebrand carefully — **RECOMMENDED**

**Method (future, not now):** Create a new working branch from local `main` (or `origin/main`), apply Monsun URL/name updates with review, gate LICENSE copyright on ownership, align website claims, keep Release data strategy.

| Criterion | Score |
|-----------|-------|
| Scientific functionality | Best |
| Educational completeness | Best |
| Reproducibility | Best (Release docs + gitignore) |
| Branding consistency | Achievable in controlled pass |
| History preservation | Excellent (no rewrite) |
| Review complexity | Moderate (brand diff review) |
| Risk of losing WIP value | Low (WIP value is strings only) |
| Long-term maintenance | Aligns with website docs |

### Strategy B — Rebuild from Monsun WIP — **REJECTED**

Would discard `905b1ba` stack; ships broken indices and hollow modules. Brand “wins” but product loses.

### Strategy C — Clean branch cherry-picking both — **UNNECESSARY**

WIP unique functional delta ≈ 0. Cherry-pick brand hunks onto main line is just Strategy A with extra process. Only revisit C if later evidence finds hidden WIP commits (none in local objects).

### Strategy D — Other

None warranted from local evidence.

---

## 9. Proposed future branch workflow (design only)

```text
main (publish)          ← full tutorial + approved Monsun brand
  \- integrate/monsun-rebrand   ← Phase 1B+ work branch from local main
dev or feature/*        ← optional experiments
add-extract-utils       ← retain as historical ref; do not delete in 1A;
                          do not merge into main as-is
fork/*                  ← personal integration remote; not SoT
```

After authorized implementation: PR into `monsun-labs/extract-point` `main`; retarget local `origin` URL; keep Release `v0.1-data`.

---

## 10. Phase 1B prerequisites and acceptance criteria

### Prerequisites (inputs)

1. Human approval of Strategy A + baseline tip (local `main` vs strictly `origin/main`).
2. Decision on LICENSE copyright holder text.
3. Authorization to run **read-only** NetCDF metadata inspection and/or controlled notebook validation (still no CDS mass download unless approved).
4. Confirm canonical URLs: `https://monsun.io/labs/`, `https://github.com/monsun-labs/extract-point`.

### Phase 1B scope (proposed)

1. NetCDF metadata validation against docs (dims, vars, units, time, size).
2. Static+optional runtime validation of main-line notebook paths against sample.
3. Produce an approved **rebrand patch list** (file-by-file) without applying until Phase 1C/implement gate.
4. Website claim checklist vs baseline tip.
5. Explicit non-goals: no merge of WIP tree; no history rewrite; no Release upload unless requested.

### Acceptance criteria for Phase 1B

- [ ] Sample file metadata documented (or PARTIAL if blocked).
- [ ] Main-line learner path marked STATICALLY consistent or RUNTIME verified per authorized tests.
- [ ] Rebrand inventory complete with LICENSE decision recorded.
- [ ] No unauthorized mutations; before/after integrity recorded.
- [ ] Clear go/no-go for implementation phase.

---

## 11. Finding IDs (decision-level)

| ID | Severity | Component | Evidence | Impact | Action | Verification | Phase |
|----|----------|-----------|----------|--------|--------|--------------|-------|
| SOT-01 | HIGH | Baseline | Ancestry + capability matrix | Wrong SoT loses tutorial | Adopt main-line baseline | reports 01–02 | 1A |
| SOT-02 | HIGH | Strategy | WIP unique commit brand-only | B/C waste or harm | Choose A | `git show 8f7b9d6` | 1A |
| SOT-03 | MEDIUM | Publish tip | om still Nusawave | Live Colab brand debt | Rebrand after lock | README | 1B |
| SOT-04 | HIGH | Legal | LICENSE diverge | Rights risk | Human confirm | counsel | 1B |
| SOT-05 | INFO | Data | Local SHA256 = Release | Provisional sample OK | Keep untracked | hash | 1A |

---

## 12. Prohibited actions — confirmation (not performed)

Phase 1A did **not**:

- Edit/create/delete/rename existing project source files (except creating these audit Markdown files under `audit/phase-1a/` on the website repo).
- Checkout, switch, reset, rebase, merge, cherry-pick, or create branches.
- Fetch, pull, push, or modify remotes/upstream/tags/git config.
- Commit or open PRs.
- Change GitHub settings, Actions, secrets, releases, issues, or metadata.
- Run notebooks, tutorial scripts, benchmarks, or ERA5 download scripts.
- Download datasets or modify `data/era5_sample.nc`.
- Install dependencies or modify Python envs.
- Change website content, workflows, DNS, or production (beyond adding audit reports).
- Fetch missing remote objects.

Read-only Git (`status`, `log`, `show`, `diff`, `merge-base`, `rev-list`, `cat-file`, `ls-tree`, `grep`) and local SHA256 of the existing NetCDF were used.

---

## 13. After-state

Recorded in the Phase 1A final response / integrity section: must match before-state for extract-point (branch, HEAD, remotes, porcelain). Audit files appear only under `monsun-labs.github.io/audit/phase-1a/`.
