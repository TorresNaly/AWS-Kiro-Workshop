# Project Structure

## Layout

```
.
├── index.html      # The entire game: markup, styles, and JavaScript
└── .kiro/          # Kiro configuration (steering files)
```

The project is a single file. All HTML, CSS, and JavaScript live in `index.html`.

## Organization within `index.html`

- `<head>` / `<style>`: page layout and visual theme (dark background, centered canvas).
- `<body>`: the title, the `#wrap` container, the `<canvas id="game">`, and a `#hint` line.
- `<script>`: one IIFE containing all game code, organized into labeled sections:
  - Game constants — tuning values (paddle, ball, bricks, lives, speed-up).
  - Game state — current `state`, and mutable objects (`paddle`, `ball`, `bricks`, `particles`, etc.).
  - Setup/reset — `makeBricks()`, `resetGame()`, `resetBall()`, `launchBall()`, `loseLife()`, `startGame()`.
  - Particles — `spawnBurst()`, `updateParticles()`.
  - Input — keyboard and mouse event listeners, `clampPaddle()`.
  - Update — `update()`: movement, wall/paddle/brick collisions, win/lose checks.
  - Render — `render()` plus helpers `drawRoundedRect()`, `drawHeart()`, `overlay()`.
  - Loop — `loop()` driven by `requestAnimationFrame`.

## Conventions

- Keep the section ordering above when adding code so related logic stays grouped.
- Prefer adding or changing a top-of-script `const` over scattering magic numbers.
- Keep update (state changes) and render (drawing) responsibilities separate.
