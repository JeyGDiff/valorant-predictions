# SolvedState — Valorant match predictions (static site)

The published front-end for **SolvedState**: a static site that predicts
professional Valorant match outcomes, ranks teams/players by Elo, and explains
how the model works. Served by **GitHub Pages** straight from this repo's `main`.

**This repo is a deploy target, not a codebase.** It contains only static HTML/CSS,
assets, and the JSON the pages fetch. There is no build step and no application
logic here. Everything is produced by the engine repo:

> ⚙️ **Engine / source of truth:** [`JeyGDiff/valorant_match_prediction`](https://github.com/JeyGDiff/valorant_match_prediction)
> (note: underscore). That repo scrapes vlr.gg, engineers features, trains the
> models, generates the JSON, and **owns the HTML source** (in its `portfolio/`
> directory).

## How content gets here

```
valorant_match_prediction (engine)
        │
        ├─ hourly CI (predict.yml) ── git push ──▶  predictions.json
        │                                            recent_predictions.json
        │                                            backtest_results.json
        │                                            allstar.json
        │                                            lifecycle.json
        │                                            neuralnet.json
        │
        └─ manual copy+push ───────────────────▶  *.html, *.css, assets,
                                                     leaderboard.json,
                                                     draft_profiles.json
                                          │
                                          ▼
                              this repo (main)  ──▶  GitHub Pages
```

- **Hourly, automatic:** the engine's `predict.yml` regenerates live predictions and
  pushes the JSON files listed above via an SSH deploy key. Each push triggers a
  Pages deployment.
- **Manual:** HTML/CSS/assets and the `leaderboard.json` / `draft_profiles.json`
  snapshots are **not** pushed by CI. Edit them in the engine repo's `portfolio/`
  dir and copy them here by hand. ⚠️ Because of this, HTML here can drift from the
  engine's `portfolio/` — treat `portfolio/` in the engine repo as canonical.

## Pages and their data

| Page | Fetches | What it shows |
|------|---------|---------------|
| `index.html` | `predictions.json`, `recent_predictions.json`, `backtest_results.json` | Upcoming-match predictions + recent accuracy |
| `leaderboard.html` | `leaderboard.json` | Team/player Elo rankings (VCT main events) |
| `allstar.html` | `allstar.json` | All-star roster |
| `lifecycle.html` | `lifecycle.json` | Prediction lifecycle walkthrough |
| `neuralnet.html` | `neuralnet.json` | Hand-written JAX neural net teaching page |
| `draft-simulator.html` | `draft_profiles.json` | Agent draft simulator |
| `architecture.html` | — | How the system works |

Shared styling lives in `solvedstate.css`; brand assets are `ss_logo.png`,
`ss_icon.png`. `teaching_backprop.mp4` is embedded on the neural-net page.

## Deployment

GitHub Pages deploys this repo's `main` on every push, using the classic
**"Deploy from a branch"** source (Settings → Pages → Source = *Deploy from a
branch*, `main` / root). There is no custom deploy workflow.

**Known issue — intermittent deploy failures.** Because the engine pushes here
~hourly, and the classic deploy has no retry/concurrency control, GitHub's Pages
backend occasionally returns `Deployment failed, try again later.` on a given push.
This is transient and **self-heals on the next hourly push** (a *new commit* clears
it — retrying the *same* commit does not). It affects only that one deploy's
freshness, never the live site, which keeps serving the last good build. A
workflow-based fix (concurrency + retry) was attempted and reverted because
migrating `build_type` while a build was in flight left the Pages backend wedged;
if retried, flip the source to *GitHub Actions* only when Pages is idle.

## Editing

- **Data looks wrong / stale:** the fix is almost always in the **engine repo**, not
  here. Regenerate the relevant JSON there.
- **HTML/CSS change:** edit `portfolio/` in the engine repo, then copy the file here
  and commit. Don't edit here without mirroring it back — the engine copy is
  canonical and will otherwise overwrite your change on the next manual sync.
