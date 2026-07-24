# Skip Rocks

A free, single-file browser toy: skip stones across the water from a first-person
view on the shore, to a soft generative ambient score. No build step, no
dependencies — just open `index.html` (or the hosted URL) and play.

## Play

Open `index.html` in a browser, or serve the repo root locally (see the top-level
[README](../../README.md)) and visit `/games/skip-rocks/`.

**Controls**
- Press near the stone and drag in the direction you want to throw, then release
  — the drag sets power, angle, and direction, mirroring a real skipping flick.
  A flatter, faster drag skips further; a steep drag mostly just plops.
- Works identically with mouse or a single finger on touch devices.
- The circular arrow button (bottom-right) switches to a different scene on
  demand; it also rotates on its own every few throws.
- The speaker icon mutes/unmutes the ambient music and sound effects.

There's no menu, no health, no enemies — just the current throw's skip count and
your best streak for the session, shown in the top-left corner.

## Scoring

Only your best skip count **for the current session** is tracked, exactly as the
game is meant to be played — a quiet toy to leave running, not a competitive
game. Nothing is saved to `localStorage` or a server, so refreshing the page (or
coming back later) resets your best back to zero. There's no leaderboard, local
or global, by design.

## Scenes

Five procedurally-drawn environments (misty dawn lake, golden afternoon pond,
autumn shoreline, alpine tarn, dusk cove) rotate automatically, each with its own
small set of decorative wildlife (ducks, dragonflies, a heron, jumping fish,
birds, fireflies, bats) — purely ambient, nothing to interact with.
