# GitHub identity & workflow — Monsun Labs website (Policy B)

**Repository:** `monsun-labs/monsun-labs.github.io`  
**Policy:** B — Website repository  
**Canonical site:** https://monsun.io/labs/

This document is **repository-specific**. Do **not** use the tutorial (Policy A / monsunic developer) workflow here.

## Exclusive account: tyorus

Use **tyorus** for all of the following in this repository:

- Development
- Git commits
- Git push authentication
- Pull Request creation
- Review
- Approval
- Manual merge

**Never** use `monsunic` for commits, pushes, PR creation, review, or merge in this repository.

Auto-merge: disabled (manual merge by tyorus).

## Local Git identity (configured in this clone)

```text
user.name  = tyorus
user.email = 78007594+tyorus@users.noreply.github.com
```

Set with `git config --local` only. Do not change this clone to monsunic.

## Authentication

- Prefer the existing `tyorus` SSH key / `git@github.com` (or host alias `git@github.com-tyorus:...`).
- `gh` operations for this repo must run as **tyorus** (`gh auth status` → active user `tyorus`).
- If you previously switched `gh` to monsunic for a tutorial PR, switch back before website work:

```bash
gh auth switch --user tyorus
```

## Pre-flight checklist (every remote write)

1. `git rev-parse --show-toplevel` ends with `monsun-labs.github.io`.
2. `git remote -v` → `monsun-labs/monsun-labs.github.io`.
3. `git config --get user.name` → `tyorus`.
4. `gh auth status` active account → `tyorus`.
5. Do not push tutorial branches or monsunic-authored commits into this repo by mistake.

## Related policy

Tutorial source repos such as `monsun-labs/extract-point` use **Policy A** (developer monsunic; review/merge tyorus). See that repository’s `GITHUB_WORKFLOW.md`.
