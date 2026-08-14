SAFe TRACKS: Leading SAFe Trivia
Real-time multiplayer quiz for the AI-Empowered Leading SAFe training.
Built by BARY with Love for the Wavestone Leading SAFe Training in August 2026.
Play it: https://y8arman.github.io/SAFe_with_BARY/
Successor to SAFe & SOUND (POPM). Same host-authoritative architecture, six stations, Wavestone CI.
What it is
Single self-contained `index.html`. Vanilla JS, no runtime build step, hosted on GitHub Pages.
Host drives the game on a shared screen, players join by QR code or a 4-letter code on their phones. Late join works at any point.
Six stations, one per workbook lesson: Disruption Depot, Values Viaduct, Marshalling Yard, Flow Cross, Portfolio Parkway, Change Terminus.
Each run deals 10 random questions from the lesson's pool of 30 (5, 10, or 15 selectable), so games are repeatable.
Scoring: 1000 points scaled by speed down to 500 at the buzzer, +100 per consecutive correct beyond the first (capped at +500), double points on the final stop.
Extras: Practice solo mode, Estimating Poker (Animal Warm-up and Story Rounds), Conductor commentary, class report on the final screen.
See `HOW-TO-PLAY.md` for the quick handbook.
Firebase setup (once)
Backend is Firebase Realtime Database (compat SDK via CDN). This app writes only to the `lst_games` branch, so it coexists with the POPM app on the same project.
Rules (Build -> Realtime Database -> Rules -> select all -> paste -> Publish). Contains both branches so the older app keeps working:
```json
{
  "rules": {
    ".read": false,
    ".write": false,
    "st_games":  { "$code": { ".read": true, ".write": true } },
    "lst_games": { "$code": { ".read": true, ".write": true } }
  }
}
```
If you move to another Firebase project, replace the FIREBASE CONFIG block in `index.html`. The `databaseURL` must be present and match the database region. Do not rely on test mode rules, they expire.
Free tier allows about 100 simultaneous connections; plan for 80.
Build pipeline
`index.html` is generated, never edited by hand:
`questions_data.js` holds the 180-question bank (30 per lesson, schema `{ q, options[2-4], answer, time }`).
`template.html` is the app with a placeholder for the bank.
`node build.js` validates the bank (counts, schema, duplicates, dashes) and injects it into `index.html`.
`node verify.js` runs the full battery: hex-outside-root guard, dash scan, DOM id cross-check, credit line, Firebase config, six-station checks, reliability kit, and re-validation of the injected bank.
`node --check` on the extracted script confirms syntax.
Test protocol (every build)
Open the deployed page, host a game, press B for 15 bots, play a full 10-stop run, and check the leaderboard animation and the final screen with all 15 visible. Never run the class on a build that has not passed this.
Version
v0.1 (2026-08-14)
