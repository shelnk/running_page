# Fork maintenance notes

This fork is based on `yihong0618/running_page` v2 and keeps the old personal data from the `self` branch.

## Publish

- The site is intended to publish with Vercel, not GitHub Pages.
- Vercel build command: `pnpm build`.
- Vercel output directory: `dist`.
- After upgrading to v2, update the Vercel project framework/build settings from Gatsby to Vite if Vercel still remembers the old configuration.
- Current site URL in code: `https://running-page.vercel.app`.

## Current Data

- Historical GPX files live in `GPX_OUT/`.
- The SQLite database lives in `run_page/data.db`.
- Frontend activity data lives in `src/static/activities.json`.
- `GPX` is the GPS track file format exported by sports apps and devices. The sync scripts read GPX/FIT/TCX or platform APIs, update `run_page/data.db`, and regenerate `src/static/activities.json` plus SVG stats.

## Sync New Data

Recommended default source is Coros:

1. Add GitHub Secrets `COROS_ACCOUNT` and `COROS_PASSWORD`.
2. Change `.github/workflows/run_data_sync.yml` `RUN_TYPE` from `pass` to `coros`.
3. Run the `Run Data Sync` workflow manually once.
4. If the result looks right, keep the scheduled workflow enabled.

Strava is also supported:

1. Create a Strava API app and store `STRAVA_CLIENT_ID`, `STRAVA_CLIENT_SECRET`, and `STRAVA_CLIENT_REFRESH_TOKEN` as GitHub Secrets.
2. Change `RUN_TYPE` to `strava`.
3. Note that Strava changed its Developer Program in June 2026; API access may require checking the app tier and subscription status.

iGPSPORT is best handled locally first:

```bash
python run_page/igpsport_sync.py <mobile> <password> --with-gpx
```

This exports GPX and updates local data. Commit the changed `GPX_OUT/`, `run_page/data.db`, `src/static/activities.json`, and generated `assets/*.svg` files after checking the site.

## Useful Local Commands

```bash
python run_page/db_updater.py
python run_page/gen_svg.py --from-db --title "Running Page" --type github --github-style "align-firstday" --athlete "shelnk" --special-distance 10 --special-distance2 20 --special-color yellow --special-color2 red --output assets/github.svg --use-localtime --min-distance 0.5
python run_page/gen_svg.py --from-db --title "Over 10km Runs" --type grid --athlete "shelnk" --output assets/grid.svg --special-color yellow --special-color2 red --special-distance 20 --special-distance2 40 --use-localtime --min-distance 10
pnpm build
```
