# SolvedState — moved

This repository was the GitHub Pages deploy target for **SolvedState**. The site
now lives at **https://solvedstate.vercel.app**, built from `portfolio/` in the
engine repo (`JeyGDiff/valorant_match_prediction`) and served by Vercel.

Every page here is a redirect stub, including `404.html`, so old deep links land
on their new counterpart.

The `*.json` files are the last data this site served (2026-08-01). They are kept
deliberately as a rollback point — data is now published to Vercel Blob and served
at `/data/...`, and `predictions_archive.json` here is the append-only prediction
history that the migration restored from.

Nothing writes to this repository any more.
