# Sonoran Shred XC dashboard

## Set up on GitHub

1. Unzip this package. Put its contents at the root of your repository, on the default branch (usually `main`). Upload the files, not the ZIP itself or an enclosing folder.
2. Include `.github/workflows/update-dashboard.yml`, `scripts/sync_bikereg.py`, `tests/test_sync.py`, and `index.html`, preserving those paths. If your upload method skips the `.github` folder, use GitHub's **Add file → Create new file**, enter `.github/workflows/update-dashboard.yml` as the filename, and paste the included workflow into it.
3. Open repository **Settings → Pages**. Set **Source** to **GitHub Actions** (not Deploy from a branch).
4. Open **Actions → Update and publish race dashboard → Run workflow**. Choose the default branch, then run it. If prompted to enable Actions, enable them first.
5. Wait for the run to succeed. Your dashboard URL appears under **Settings → Pages** and in the deployment step.

No personal access token, BikeReg account, npm install, or API key is required. The workflow uses GitHub's built-in token to deploy. GitHub Pages must be available for your repository/account, and repository or organization policies must permit Actions and Pages deployment. The included workflow automatically runs only on your repository's default branch.

## Updates

- BikeReg event: https://www.bikereg.com/Confirmed/76688
- GitHub checks at minutes 7, 22, 37, and 52 each hour. This is approximately every 15 minutes, not instant. GitHub can delay or skip scheduled runs under load. A workflow in an inactive public repository can be disabled after 60 days without repository activity; re-enable it from Actions if needed.
- Open pages check for the latest published feed once per minute. The displayed sync timestamp is the last successful BikeReg fetch.
- Common University of Arizona abbreviations and the known “Univeristy” typo match automatically. Uncertain Arizona team names appear under **Officer tools → Team matches to review**; Arizona State and Northern Arizona are excluded.
- If BikeReg changes its undocumented endpoint, query, or categories, the workflow may need maintenance. Incomplete responses, errors, and zero team matches fail the run instead of clearing the dashboard. Check the failed run in Actions; the previously published dashboard remains available.
- The workflow deploys generated files directly. It does not commit registration changes to your source repository. The source `index.html` remains the officer-maintained baseline; deployed registrations are refreshed on each successful run.

## Volunteer records, design, and team-match decisions

Open **Officer tools** to edit volunteer records, shifts, notes, colors, headings, and logo. These changes save in your browser. Automatic registration refreshes preserve them. Newly matched riders are added as “Not completed”; existing riders are never removed from the roster just because their registration disappears.

To make your officer changes visible to everyone:

1. Click **Download updated HTML**.
2. Replace the repository's root `index.html` with that download and commit it to the default branch.
3. The workflow fetches registrations again and publishes your updated dashboard.

Keep the workflow, script, and tests in the repository when replacing `index.html`. The downloaded HTML includes the feed integration. Use the latest deployed dashboard when editing, and keep a backup before switching devices. Browser-local edits on another device are not shared or merged automatically.

Anyone can open officer tools and change their own local copy. Only people with repository write/deployment access can publish changes for everyone. This does not add a secure administrator login or shared database. All data included in a published HTML file, including notes, is public.

The CSV importer remains available as a temporary fallback, but the next successful automatic feed refresh replaces manual registration imports. Volunteer records remain intact. Local file previews work without a server but cannot retrieve the hosted registrations feed.

## Files and checks

- `index.html`: dashboard, embedded baseline data, and design.
- `.github/workflows/update-dashboard.yml`: schedule, tests, fetch, and Pages deployment.
- `scripts/sync_bikereg.py`: dependency-free fetch and validation; writes `public/` for deployment.
- `tests/test_sync.py`: team matching, invalid-response handling, officer-data preservation, and safe HTML embedding.

Local checks: `python -m unittest discover -s tests -v`

Local fetch/build: `python scripts/sync_bikereg.py`

Official GitHub guidance:
- https://docs.github.com/en/pages/getting-started-with-github-pages/using-custom-workflows-with-github-pages
- https://docs.github.com/en/actions/reference/workflows-and-actions/events-that-trigger-workflows#schedule
