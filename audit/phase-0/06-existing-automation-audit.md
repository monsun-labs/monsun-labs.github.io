# 06 — Existing Automation Audit

**Phase:** 0 — Discovery & Inventory  
**Date:** 2026-11-27  
**Mode:** Read-only (no workflow edits; secret **values** never read)

---

## 1. Inventory of workflows

### A. `monsun-labs/monsun-labs.github.io`

| Workflow | Path | State | Triggers |
|----------|------|-------|----------|
| MonsunLab (`docs.yml`) | `.github/workflows/docs.yml` | active | `push` to `main`/`master` |
| pages-build-deployment | GitHub dynamic | active | `gh-pages` updates |

### B. `monsun-labs/extract-point`

| Workflow | Path | State |
|----------|------|-------|
| `colab-badge.yml` | `.github/colab-badge.yml` | **Empty file (0 bytes)** — not a real workflow |

No `.github/workflows/*.yml` found on published `main`.

### C. Host site (local clone `monsun.github.io` → `Monsun-Intelligence/Monsun-Intelligence.github.io`)

| Workflow | Role |
|----------|------|
| `deploy-labs.yml` | On `repository_dispatch: labs-updated` or `workflow_dispatch`: checkout Labs repo, `zensical build`, copy `site/*` → `labs/`, commit push |
| `deploy.yml` | Also clones Labs repo, builds, copies into `labs/` (broader site deploy path) |

---

## 2. Deep dive — `docs.yml`

```yaml
permissions:
  contents: write
```

| Step | Behavior |
|------|----------|
| checkout | `fetch-depth: 0` |
| setup-python | `3.x` |
| Install Zensical | `pip install zensical` |
| Build | `zensical build --clean` |
| Deploy redirect | Force-checkout `gh-pages`, delete all non-git files, copy `redirect/index.html` + `404.html`, write `CNAME=labs.monsun.io`, commit, **`git push origin gh-pages --force`** |
| Trigger host | If `secrets.MONSUNIC_DEPLOY_TOKEN != ''`, POST `repository_dispatch` `labs-updated` to `monsunic/monsunic.github.io` |

### Security / reliability notes

| ID | Severity | Finding | Evidence | Impact | Remediation | Phase |
|----|----------|---------|----------|--------|-------------|-------|
| CI-01 | **HIGH** | Latest redirect-oriented run failed | Run `32562149955` conclusion=failure, duration 0s, empty jobs, log not found | `gh-pages` never replaced with redirect stubs | Re-run/fix workflow; inspect Actions enablement & permissions | 1 |
| CI-02 | **HIGH** | Force-push to `gh-pages` | `git push --force` | History wiped; mistakes hard to roll back | Prefer clean orphan publish without needless force; protect branch | 2 |
| CI-03 | MEDIUM | `contents: write` on default `GITHUB_TOKEN` | Workflow permissions | Broad write on repo | Split jobs: build (read) vs deploy (write); environment protection | 2 |
| CI-04 | MEDIUM | Secret name `MONSUNIC_DEPLOY_TOKEN` | Conditional dispatch | Legacy naming; if unset, **canonical monsun.io/labs may not update** from this workflow | Confirm secret present; rename later; add failure visibility | 1 |
| CI-05 | MEDIUM | No concurrency group / cancel-in-progress | Absent | Overlapping pushes possible | Add `concurrency` | 2 |
| CI-06 | LOW | Build artifact not uploaded | Only used indirectly | Host rebuilds from source anyway via dispatch | Optional artifact for debugging | 2 |
| CI-07 | INFO | Builds on every push to main | Trigger list | Fine for small docs repo | Keep | — |
| CI-08 | INFO | No PR-safe preview workflow | Absent | Untrusted PRs shouldn't dispatch | Add PR build-without-deploy | 2 |

**Secret values:** not retrieved. Only name `MONSUNIC_DEPLOY_TOKEN` observed.

---

## 3. Deep dive — host `deploy-labs.yml`

| Item | Value |
|------|-------|
| Trigger | `repository_dispatch` type `labs-updated`; `workflow_dispatch` |
| Permissions | `contents: write` |
| Labs source | `monsun-labs/monsun-labs.github.io` @ `main` |
| Host checkout ref | `main` |
| Build | `pip install zensical` + `zensical build --clean` |
| Publish | Replace `labs/`, `.nojekyll`, commit, `git push origin HEAD:main` |
| Skip | If no staged diff, exit 0 |

| ID | Severity | Finding | Impact | Phase |
|----|----------|---------|--------|-------|
| CI-09 | MEDIUM | Host default branch is `dev` but workflow uses `main` | May update non-default branch | 1 |
| CI-10 | INFO | Rebuilds Labs from source instead of consuming prebuilt `site/` | Extra CI time; ensures fresh build | — |
| CI-11 | INFO | Duplicate path with `deploy.yml` also building Labs | Two entry points — document which is authoritative | 1 |

---

## 4. Capability for future tutorial publishing

| Need | Supported today? |
|------|------------------|
| Build & deploy website docs | **Partial** — host path works (live site up); Labs→dispatch fragile; gh-pages redirect broken |
| Sync tutorial README → docs landing | **No** |
| Sync media/thumbnails | **No** |
| Validate notebook / deps | **No** |
| PR-based human review of site changes | Manual only |
| Tutorial repo CI | **None** (empty stub) |
| Idempotent media manifest | **No** |
| Untrusted PR isolation | Not designed |

**Finding CI-12 (HIGH):** Automation today is **site-build/deploy only**. It cannot yet implement “tutorial repo → package → website → Zensical → deploy” without new workflows and a content contract.

---

## 5. Observed run history (Labs site)

| When | Event | Result |
|------|-------|--------|
| 2026-08-22 | push `add redirection` / `docs.yml` | **failure** |
| 2026-08-05 | pages-build-deployment | success |
| 2026-08-04 | Rebrand + docs.yml | success |
| 2026-08-04 | pages-build-deployment | success |

Successful Aug 4 run explains why `gh-pages` still holds a **full rebranded site**, not redirects.

---

## 6. Recommendations (design only)

1. Repair `docs.yml` redirect job and verify `labs.monsun.io` → `monsun.io/labs/`.
2. Confirm `MONSUNIC_DEPLOY_TOKEN` (or successor) and dispatch target repo name.
3. Add PR workflow: build Zensical, upload artifact, **no** dispatch / no gh-pages write.
4. Keep publish to production behind `main` + human review.
5. Tutorial publishing (Phase 2+): separate reusable workflow — see report 07.
