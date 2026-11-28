# Labs publish architecture

Notebook source of truth lives in tutorial repos. Automation converts committed
notebook outputs to Markdown + media, then opens a **pull request** against this
website repository. Humans merge (Policy B: **tyorus**). Existing `docs.yml`
deploy is unchanged.

Canonical site: https://monsun.io/labs/

## Flow

1. Tutorial repo (`workflow_dispatch` or release) runs `.github/workflows/labs-publish.yml`
2. Reusable workflow [`.github/workflows/labs-publish-reusable.yml`](.github/workflows/labs-publish-reusable.yml) converts with **no execute**
3. Branch `publish/<slug>` is created/updated on this repo
4. PR opened or commented (idempotent per slug)
5. tyorus reviews and merges
6. `docs.yml` builds Zensical and dispatches `labs-updated` to the host

## Authentication (cross-repo)

`GITHUB_TOKEN` cannot open PRs on another repository. Use one of:

| Mechanism | Recommendation |
|-----------|----------------|
| **GitHub App** installation token | Preferred — short-lived, least privilege, install only on Labs repos |
| Fine-grained PAT owned by **tyorus** | Interim — store as `LABS_PUBLISH_TOKEN` on each tutorial repo |

### GitHub App (preferred)

1. Register App under `monsun-labs` (or user-owned installed on the org)
2. Permissions: website `Contents: Read and write`, `Pull requests: Read and write`; tutorial repos `Contents: Read-only`
3. Install on `monsun-labs.github.io` + tutorial sources
4. In each tutorial repo (or org secrets), set:
   - Either mint an installation token in the caller and pass as `LABS_PUBLISH_TOKEN`
   - Or use `actions/create-github-app-token` then map to `PUBLISH_TOKEN`

### Interim PAT

Create a fine-grained PAT as **tyorus** limited to `monsun-labs/monsun-labs.github.io` with Contents + Pull requests write. Store as repository secret `LABS_PUBLISH_TOKEN` on the tutorial repo. Rotate when leaked or unused.

**Never** expose publish tokens to `pull_request` events from forks.

## Security checklist

- [ ] Publish token not available to fork PR workflows
- [ ] Converter never executes notebooks
- [ ] Media path allowlist enforced via tutorial `labs-publish/manifest.yaml`
- [ ] Automation never merges website PRs
- [ ] No CDS / `cdsapi` secrets in website CI
- [ ] Idempotent branch `publish/<slug>` (one open PR per slug)
- [ ] Policies A (tutorials) and B (website) unchanged

## Second-tutorial readiness

New tutorial repos should copy:

- `labs-publish/manifest.yaml` (edit slug/paths)
- `labs-publish/convert_notebook.py`, `apply_to_website.py`, `requirements.txt`
- `.github/workflows/labs-publish.yml` (same reusable call)
- Secret `LABS_PUBLISH_TOKEN` (or App token minting)

Do not add website nav rows until the tutorial repo exists and a publish PR has been reviewed.
