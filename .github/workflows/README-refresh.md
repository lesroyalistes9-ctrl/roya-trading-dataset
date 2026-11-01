# Keeping this repository in sync with the live endpoints

This file explains the sync; it does not implement it. Nothing here runs by itself until you create a real workflow file next to it (e.g. `refresh.yml`) — do that only once the repository exists and you have read the commands below.

## What "sync" means

The five files under `data/` are byte-for-byte copies of the live endpoints of roya-trading.com. The endpoints are regenerated at every deploy of the site (field `generatedAt`), so the repository lags the site by at most one sync run. The repository never edits the data: it re-downloads it. `data/READ_AT.txt` records when.

## 1. Sync by hand (paste in a terminal, from the repository root)

```sh
set -e
STAMP="$(date -u +%Y-%m-%dT%H:%M:%SZ)"
for f in registry.json registry.csv registry.schema.json payouts.json payouts.csv; do
  curl -fsSL "https://roya-trading.com/api/$f" -o "data/$f"
done
{
  echo "Fetched from the live endpoints with curl (HTTP 200 each) on $STAMP."
  echo
  for f in registry.json registry.csv registry.schema.json payouts.json payouts.csv; do
    printf '  https://roya-trading.com/api/%-22s %8s bytes\n' "$f" "$(wc -c < "data/$f")"
  done
  echo
  echo "These copies are snapshots. The endpoints above are regenerated at every deploy of roya-trading.com; when they differ from these files, the endpoints win."
} > data/READ_AT.txt
git add data && git commit -m "data: sync from roya-trading.com ($STAMP)" || echo "nothing changed"
git push
```

`curl -f` makes a non-200 answer fail the script, so a broken endpoint never overwrites a good file with an error page.

## 2. The same thing on a schedule with GitHub Actions (explanation, not installed)

Create `.github/workflows/refresh.yml` with a `schedule` trigger (`cron: "30 4 * * *"` = 04:30 UTC daily, i.e. after the site's own 10:00 Bangkok build/deploy, which is 03:00 UTC) plus `workflow_dispatch` for manual runs. The job needs `permissions: contents: write`, checks out the repo, runs the exact block of section 1 (without the `git push`, which the action does with the built-in `GITHUB_TOKEN`), and commits only when `git diff --quiet -- data` is false.

Use `git config user.name "roya-data-bot"` / `git config user.email "…"` before committing so the history shows the sync commits distinctly. Do not store any secret: the endpoints are public and read-only.

## 3. Cutting a release (for the Zenodo DOI)

Zenodo archives a GitHub *release*, not a commit. After a sync you want to cite: `git tag -a v1.5-2026-11-01 -m "Registry v1.5, snapshot 2026-11-01"` then `git push --tags`, then create the release from that tag in the GitHub UI. Zenodo (once the repository is enabled there) mints a DOI for each release and a concept DOI for the whole series; paste the concept DOI into `README.md` (citation block) and into `zenodo.json` is not needed — `zenodo.json` is read by Zenodo at release time.

Tag naming suggestion: `v<registry version>-<snapshot date>`, so the tag alone says which format and which day.

## 4. Hugging Face mirror

The `huggingface/README.md` dataset card expects the same `data/` folder next to it. Simplest: keep the HF dataset repo as a second remote and push the same commit (`git remote add hf https://huggingface.co/datasets/<org>/<name>` then `git push hf main`), or upload the five files through the HF web UI after each release. The card's `configs` point to `data/registry.csv` and `data/payouts.csv`.
