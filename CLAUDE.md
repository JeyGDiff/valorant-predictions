# CLAUDE.md

## What this repo is

The **published static site** for SolvedState (Valorant match predictions), served by
GitHub Pages from `main`. It is a **deploy target, not a codebase** — only HTML/CSS,
assets, and JSON. There is no build step and no application logic here.

**Before changing anything, know where the source of truth lives:**

- **HTML / CSS / assets** are generated in the **engine repo**,
  `JeyGDiff/valorant_match_prediction` (underscore — this repo is
  `valorant-predictions`, dash), under its `portfolio/` directory. The copy here is a
  deployed snapshot. **Editing HTML here without mirroring the change back into the
  engine's `portfolio/` will be silently overwritten** on the next manual sync. Prefer
  editing there.
- **Data JSON** is produced by the engine's scripts. If prediction/leaderboard/etc.
  data looks wrong or stale, the bug is in the engine, not here — fix and regenerate
  there. Don't hand-edit the JSON.

## How content arrives

- **Automatic (hourly):** the engine's `.github/workflows/predict.yml` pushes
  `predictions.json`, `recent_predictions.json`, `backtest_results.json`,
  `allstar.json`, `lifecycle.json`, `neuralnet.json` here via an SSH deploy key. Each
  push triggers a Pages deploy.
- **Manual:** HTML/CSS/assets and `leaderboard.json` / `draft_profiles.json` are copied
  over by hand from the engine's `portfolio/`. CI does **not** touch these.

## Deployment

Pages uses the classic **"Deploy from a branch"** source (`main` / root) — no custom
workflow. Every push deploys.

Deploys intermittently fail with `Deployment failed, try again later.` in the
`deploy` step. This is a **transient GitHub Pages backend error**, not a data bug (a
data/content problem fails the *build* step; here the build succeeds and only the
deploy fails). It **self-heals on the next hourly push** — a *new commit* clears it;
retrying the *same commit* does not (deploy IDs are keyed by commit SHA). Don't
"fix" it by editing content. A workflow-based deploy (concurrency + retry) was tried
and reverted: switching `build_type` to `workflow` mid-build wedged the backend into
a persistent `errored` state. If attempting again, flip the Pages source only when
Pages is fully idle.

## Page → data map

`index.html` ← predictions.json + recent_predictions.json + backtest_results.json ·
`leaderboard.html` ← leaderboard.json · `allstar.html` ← allstar.json ·
`lifecycle.html` ← lifecycle.json · `neuralnet.html` ← neuralnet.json ·
`draft-simulator.html` ← draft_profiles.json · `architecture.html` = static.

Shared styles: `solvedstate.css`. Brand: `ss_logo.png`, `ss_icon.png`.

## Guidelines

Keep changes surgical. Don't add tooling, frameworks, or a build step — this repo is
intentionally a flat static bundle. Match the existing HTML/CSS style. When in doubt
about where a change belongs, it belongs in the engine repo.
