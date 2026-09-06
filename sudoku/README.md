# Sudoku

Part of [Puzzle Hub](../README.md), served at `puzzles.souravkhoso1.com/sudoku/`.

## How it works

- **Puzzles live in `puzzles/YYYY-MM-DD.json`.** Each file has the puzzle
  grid, the solution grid, and a difficulty label. `puzzles/manifest.json`
  lists every date that's available so the front end knows what to fetch.
- **`scripts/sudoku-core.js`** generates a full valid grid and carves cells
  out of it one at a time, backing off any removal that would create a
  second solution — every puzzle has exactly one solution. Generation is
  seeded from the date string, so regenerating a given date always produces
  the same puzzle.
- **`scripts/generate-daily-puzzle.js`** writes new puzzle files, rebuilds
  `manifest.json`, and rebuilds this folder's `sitemap.xml` (referenced
  from the hub's root `sitemap.xml` as a sitemap index entry). Run from
  the hub's nightly workflow (`.github/workflows/daily-puzzles.yml`).
- **Progress is saved in `localStorage`**, keyed by puzzle date, so it
  survives a page reload but never leaves the visitor's device.
- **Theme** is light/dark/system, remembered in `localStorage`.
- **Date navigation** (prev/next arrows + a date picker) lets visitors
  browse any past puzzle via `?date=YYYY-MM-DD`.

## Generating puzzles locally

```bash
# From the repo root:

# Today's puzzle only
node sudoku/scripts/generate-daily-puzzle.js

# A specific date
node sudoku/scripts/generate-daily-puzzle.js 2026-09-01

# Bootstrap a bunch of days at once (e.g. the next 30)
node sudoku/scripts/generate-daily-puzzle.js --days-ahead 30
```

## Notes / things you might want to tweak

- **Difficulty rotation** is day-of-week based (see `difficultyForDate` in
  `scripts/sudoku-core.js`) — easy early week, medium Thu/Fri, hard on
  weekends. Change the clue counts in `DIFFICULTY_CLUES` or the rotation
  logic to taste.
- **Timezones**: the site picks "today" based on the visitor's local date,
  falling back to the most recent available puzzle if today's hasn't been
  generated yet. There's no server, so this is the tradeoff for keeping
  the site fully static.
- **Mistake limit** is 3, defined as `MISTAKE_LIMIT` in `js/app.js`.
- **Certificate download** draws a PNG on a hidden `<canvas>` and triggers
  a browser download — no server round-trip.

## Migrated from sudoku.souravkhoso1.com

This used to be its own repo/domain (`sudoku-challenge`,
`sudoku.souravkhoso1.com`). It's now `/sudoku/` under the Puzzle Hub domain.
The old domain should redirect here (preserving `?date=` query strings)
rather than being torn down, to avoid breaking existing search rankings
and bookmarks.
