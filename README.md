# 🐄 Cow Chase: Make the C Code Faster

A single-file browser game that teaches how to make C code run faster. Players edit a small C program, and a built-in mini interpreter counts its CPU cycles. Fewer cycles means a faster runner. Outrun the cow to clear the problem.

Teams of two register first, and their names and scores are saved to a Google Sheet.

## Features

- **Team registration.** Team name plus two players, recorded in Google Sheets. Team names must be unique.
- **5 problems, 10 points each.** 50 points maximum, saved live to the sheet after each cleared problem.
- **Mini C interpreter with a cost model.** No install, no server, no compiler. Everything runs in the browser.
- **Instant feedback on submit.** Compiled, tests passed, goal reached, and a full speed report (cycles, speed-up, cache hits, wrong guesses).
- **Cycle meter.** Bars split cycles into memory waits, wrong branch guesses and math.
- **Race animation.** The runner's speed is set by the cycle count. Beat the cow's 9 m/s to escape.
- **Escaped popup.** Stars, points earned, team score, with **Retry** and **Next** buttons.
- **Hints.** Three spoiler levels per problem.
- **Progressive unlock.** Each problem unlocks after the previous one is cleared.

## The problems

| # | Problem | Concept |
|---|---------|---------|
| 1 | Column Walk | Cache and locality |
| 2 | Slow Math | CPU performance equation |
| 3 | Grade Buckets | Branch prediction |
| 4 | Two Loops | Amdahl's Law |
| 5 | Sensor Grid | Boss level: everything at once |

## Scoring

- Clearing a problem (escaping the cow) is worth **10 points**.
- The score is capped at **50 points** across the 5 problems.
- Re-clearing a problem does not add points.
- Stars: ★ for escaping, ★★★ for near-perfect cycle counts. Stars do not change the score.

## Cycle costs

| Operation | Cycles |
|-----------|--------|
| add, compare | 1 |
| multiply | 3 |
| divide, modulo | 10 |
| cache hit | 1 |
| cache miss | 20 |
| wrong branch guess | +8 |

The mini C supports `int`, global arrays, `for`, `while`, `if`, and code inside `main()`. It does not support pointers, `printf`, floats or extra functions.

## Getting started

1. Download or clone this repository.
2. Open `index.html` in a browser. There is nothing to build or install.
3. To host it online, enable **GitHub Pages** (Settings → Pages → deploy from the `main` branch).

The game plays without the Google Sheet, but registrations and scores are only saved once you connect one.

## Connecting the Google Sheet

1. Create a new Google Sheet.
2. Open **Extensions → Apps Script** from inside that sheet.
3. Replace the default code with the contents of `google-apps-script.gs`, then save.
4. Click **Deploy → New deployment → Web app**.
   - Execute as: **Me**
   - Who has access: **Anyone**
5. Authorize access when asked. If Google says the app is unverified, choose **Advanced → Go to (project) (unsafe)**. This is expected for your own script.
6. Copy the **Web app URL** (it ends in `/exec`).
7. In `index.html`, paste it into:

   ```js
   const SHEET_URL = 'https://script.google.com/macros/s/XXXX/exec';
   ```

8. Open the `/exec` URL in a browser. You should see `{"ok":true,"message":"Cow Chase endpoint is running"}`.

The script creates a **Teams** tab automatically:

| Team name | Player 1 | Player 2 | Total score | Last updated | Team ID |
|-----------|----------|----------|-------------|--------------|---------|

Each team has one row, which updates as they score.

> **Updating the script later:** saving the code is not enough. Use **Deploy → Manage deployments → ✏️ Edit → Version: New version → Deploy**, or the old version keeps running.

## Running at an event

- Open the game on each computer. The first screen is the registration form.
- The game remembers the team in the browser, so a refresh does not ask again.
- Before the next team plays on the same computer, click **Switch team** in the header. This clears that device's progress and shows the form again.
- Sort the **Total score** column (Z → A) in the sheet for a live leaderboard.

## Save status

The header shows whether the score reached the sheet:

| Message | Meaning |
|---------|---------|
| ☁ Saved to sheet | Confirmed by the sheet |
| ⚠ Sent, but not confirmed | The request went out, but the reply could not be read. Check the deployment |
| ⚠ Sheet not connected | `SHEET_URL` is empty |
| ⚠ Not saved yet | Offline or the script failed. Use **Retry save** |

## Troubleshooting

- **Nothing appears in the sheet.** Check that `SHEET_URL` is set, "Who has access" is **Anyone**, and the script was opened from inside that sheet.
- **The `/exec` page shows an older message.** A new version has not been deployed. See the note above.
- **The registration form does not appear.** The browser remembers a previous team. Click **Switch team**, or run `localStorage.clear()` in the browser console.
- **"That team name is already registered."** Team names are unique, ignoring case. Choose a different name.

## Project structure

```
index.html            # the whole game (HTML, CSS, JS, interpreter)
google-apps-script.gs # backend that writes to Google Sheets
README.md
```

## Notes

- The Web app URL is visible in the page source. The script limits what it accepts: it trims and caps text, blocks spreadsheet formula injection, and caps scores at 50. It is designed for classroom and event use, not for high-stakes competitions.
- Progress and the team are stored in the browser's `localStorage`.
- Add `?unlock` to the page URL to unlock all problems for testing.

## Customizing

- **Problems:** edit the `PROBS` array in `index.html`. Each has a starter program, a reference solution, hints, and a goal multiplier.
- **Points per problem:** change `PTS`. Update `MAX_SCORE` in `google-apps-script.gs` to match.
- **Cow speed:** change `K`.

## License

Choose a license for your repository, for example MIT.
