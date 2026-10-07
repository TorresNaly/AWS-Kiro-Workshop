# Tech Stack

## Core

- **HTML5** — single `index.html` page
- **CSS** — inline `<style>` block (no external stylesheets)
- **Vanilla JavaScript** — plain ES5/ES6, no frameworks or libraries
- **Canvas 2D API** — all rendering done via `canvas.getContext("2d")`

## Conventions

- No build system, no package manager, no dependencies. Everything lives in `index.html`.
- JavaScript runs inside an IIFE with `"use strict"` to avoid polluting the global scope.
- Rendering is driven by a `requestAnimationFrame` loop (`update` → `updateParticles` → `render`).
- Game logic is organized as plain functions and mutable module-scoped state objects (`paddle`, `ball`, `bricks`, etc.).
- Constants (sizes, speeds, colors) are declared in UPPER_SNAKE_CASE near the top of the script.
- Game states are tracked via a `STATE` enum object (`start`, `playing`, `over`, `win`).

## Common Commands

There is no build, compile, or test step.

- **Run / play:** open `index.html` directly in a web browser, or serve the folder with any static server, e.g.:
  ```bash
  python3 -m http.server
  ```
  then open http://localhost:8000
