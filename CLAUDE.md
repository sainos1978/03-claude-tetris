# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Running the game

No build step or dependencies. Open directly in a browser:

```bash
open index.html          # macOS
python3 -m http.server 8000  # then visit http://localhost:8000
```

## Architecture

Three files, no framework:

- **`index.html`** — DOM structure: `<canvas id="board">` (300×600 px), `<canvas id="next-canvas">` for the next-piece preview, HUD elements (`#score`, `#lines`, `#level`), and `#overlay` for pause/game-over states.
- **`style.css`** — Dark/retro theme using flexbox, CSS variables, and `backdrop-filter` on overlays.
- **`game.js`** — All game logic (~305 lines, `'use strict'`, no modules).

### game.js internals

| Concern | Key identifiers |
|---|---|
| Board state | `board` — `ROWS×COLS` matrix; `0` = empty, `1–7` = piece color index |
| Piece definitions | `PIECES[]` — square matrices; `COLORS[]` — hex strings indexed 1–7 |
| Rotation | `rotateCW(shape)` — transpose + reverse; `tryRotate()` — applies wall kicks `[0,±1,±2]` |
| Collision | `collide(shape, ox, oy)` — bounds + board overlap check |
| Game loop | `loop(ts)` via `requestAnimationFrame`; accumulates `dropAccum` against `dropInterval` |
| Locking | `lockPiece()` → `merge()` → `clearLines()` → `spawn()` |
| Scoring | Classic table `[0,100,300,500,800]` × `level`; hard drop +2/row, soft drop +1/row |
| Speed | `dropInterval = max(100, 1000 − (level−1) × 90)` ms; level up every 10 lines |
| Ghost piece | `ghostY()` projects current piece down; drawn at `globalAlpha = 0.2` |

### Tunable constants (top of game.js)

`COLS`, `ROWS`, `BLOCK`, `COLORS`, `LINE_SCORES` — if you change canvas dimensions, also update `width`/`height` on `<canvas id="board">` in `index.html` to match `COLS×BLOCK` and `ROWS×BLOCK`.
