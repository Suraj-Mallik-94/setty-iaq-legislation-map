# SETTY IAQ legislative map — Squarespace integration

## Included files

- `squarespace-map.html`: Existing map with external JSON loading, local fallback, latest-check marker, tracked bill details.
- `data/iaq-data.json`: Initial source data extracted **without legal verification** from the existing SETTY map (18 states; others use original baseline logic).
- `data/watchlist.json`: Exact bills configured for daily tracking: NY, RI, MA.
- `scripts/update_feed.py`: Daily LegiScan API monitor for the listed bills. Does **not** make legal judgments about enactment, effective dates, obligations or applicable building types.
- `.github/workflows/update-iaq.yml`: GitHub Actions daily scheduler and data publication.

## Set up the service

1. Create a **public GitHub repository** for the public JSON feed. Upload the folder's contents, retaining `.github/workflows/...` and other paths.
2. Create a [LegiScan](https://legiscan.com/legiscan) API account/key. Confirm your plan permits the intended frequency, usage, and public display.
3. GitHub repository **Settings → Secrets and variables → Actions → New repository secret**: name `LEGISCAN_API_KEY`, value your key. Do not insert the key into Squarespace HTML or public JSON.
4. In **Settings → Actions → General**, ensure workflow write access is permitted (`Read and write permissions`, if required). Enable GitHub Actions.
5. In **Settings → Pages**, set `Deploy from a branch`, `main`, `/ (root)`. The feed will be `https://YOUR-GITHUB-USERNAME.github.io/YOUR-REPOSITORY/data/iaq-data.json` once Pages is published.
6. Open `squarespace-map.html` and replace the value of `IAQ_FEED_URL` with the live URL from step 5.
7. On Squarespace, **back up the existing Code Block**. Edit the IAQ page, add/replace an HTML Code Block, paste all the contents of `squarespace-map.html` (Display Source off), save, and test on a public/incognito page. JavaScript in Code Blocks requires an eligible Squarespace plan.
8. Run `Check tracked IAQ bills` manually from **Actions → Run workflow**. Examine the run logs. Confirm `publishedAt` changes in the live JSON and the check indicator appears on the map. The workflow runs daily at approximately 07:17 UTC, subject to GitHub scheduler delays.

## What is and is not automatic

- **Automatic:** daily retrieval of bill activity from LegiScan for the explicit watchlist, publishing the results as public JSON, displaying those results in state popups, and loading the newest JSON on visits without re-editing Squarespace.
- **Not automatic:** classification of a bill as a legally effective statute, updates to all state summaries/requirements, identifying all newly introduced relevant bills, interpreting jurisdictional exceptions, compliance assurance, or validation of the initial data. `Passed` in LegiScan is not necessarily a signed/enacted law.
- **Recommended to add next:** comprehensive full-text discovery across jurisdictions and a human legal-review approval workflow before changing state-level map colors and legal descriptions. This needs API budget, curation and reliable source-specific rules.
- A failed or incomplete daily run **does not advance the last-successful-check timestamp**. The map uses embedded data if the public feed is unreachable.
- The 18 initial state records are **not independently validated** and should be reviewed before treating as current compliance guidance.

## Security and operational cautions

- GitHub Pages publishes the JSON publicly, as expected for a public-facing map. Never put credentials or private data into the repository.
- GitHub Actions scheduled workflows may be delayed or disabled due to repository inactivity. Monitor failure notices periodically.
- The browser's `fetch` requires the JSON URL to be accessible and permit cross-origin requests. GitHub Pages normally sends appropriate CORS headers for public static assets; verify in-browser.
- If LegiScan rate limits cause failures, reduce the watchlist/check frequency or increase your plan.
- Consider an external uptime monitor and change-approval process for authoritative legal guidance.
