# Tech Stack

## Overview

- Plain vanilla JavaScript (ES5-style, wrapped in an IIFE with `"use strict"`). No frameworks.
- HTML5 `<canvas>` 2D context for all rendering.
- CSS is inline in a `<style>` block within `index.html`.
- No build tooling, package manager, bundler, transpiler, or external dependencies.

## Running

There is nothing to build or compile. Open the game directly in a browser:

```bash
open index.html
```

Or serve it over a local HTTP server (useful to avoid any browser file:// quirks):

```bash
python3 -m http.server 8000
# then visit http://localhost:8000
```

## Testing

There is no automated test setup. Verify changes by playing the game in the browser:
check paddle movement (keys and mouse), ball launch/bounce, brick collisions, speed-up,
lives/game-over, win state, and pause.

## Conventions

- Keep everything self-contained in `index.html` unless the project is explicitly restructured.
- All game logic lives inside the single IIFE in the `<script>` block.
- Game tuning values are defined as `const` constants at the top of the script (e.g. `PADDLE_W`, `BASE_SPEED`, `ROWS`, `COLS`). Adjust gameplay by editing these rather than hardcoding values inline.
- Rendering uses the canvas 2D API directly; keep draw code inside `render()` and helpers.
- The game loop runs via `requestAnimationFrame` with an `update()` / `render()` split.
