# Second-tutorial readiness checklist

Use this when onboarding a new Monsun Labs tutorial repo.

## In the tutorial repo

1. Copy from `extract-point`:
   - `labs-publish/manifest.yaml` (edit slug, title, paths, media allowlist)
   - `labs-publish/convert_notebook.py`
   - `labs-publish/apply_to_website.py`
   - `labs-publish/requirements.txt`
   - `labs-publish/README.md`
   - `.github/workflows/labs-publish.yml`
2. Commit notebook **with outputs** you want on the website (CI will not execute).
3. Configure secret `LABS_PUBLISH_TOKEN` (App installation token preferred).
4. Dry-run locally: `python labs-publish/convert_notebook.py`
5. `workflow_dispatch` the Labs publish workflow → website PR on `publish/<slug>`

## In the website repo (after publish PR)

1. Review generated `docs/tutorials/<slug>/body.md` + `media/`
2. Add curated `docs/tutorials/<slug>/index.md` if `replace_index: false`
3. Add nav entry in `zensical.toml` and a row in `docs/tutorials/index.md`
4. Merge only after Policy B review (tyorus)
5. Do **not** invent a publication schedule table

## Non-goals

- Auto-merge
- Notebook execution in CI
- Syncing NetCDF / `.ipynb` into the website repo
