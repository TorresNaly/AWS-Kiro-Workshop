# Project Structure

```
AWS-Kiro-Workshop/
├── index.html        # The entire game: markup, styles, and logic
└── .kiro/
    └── steering/      # AI assistant guidance (this folder)
```

## Organization

This is a **single-file project**. All HTML, CSS, and JavaScript live in `index.html`:

- `<head><style>` — page layout and game chrome (canvas border, colors, fonts)
- `<body>` — `<canvas id="game">` element plus title and hint text
- `<script>` — the full game inside one IIFE

## Code layout inside the script

The script is organized top-to-bottom in these sections (marked with `// ----` comments):

1. **Game constants** — paddle/ball/brick dimensions, speeds, colors
2. **Game state** — module-scoped variables and the `STATE` enum
3. **Setup functions** — `makeBricks`, `resetGame`, `resetBall`, `launchBall`
4. **Particles** — `spawnBurst`, `updateParticles`
5. **Input** — keyboard and mouse event listeners, `clampPaddle`
6. **Update** — `update` (physics, collisions, scoring)
7. **Render** — `render` and drawing helpers (`drawRoundedRect`, `drawHeart`, `overlay`)
8. **Loop** — `loop` with `requestAnimationFrame`

## Conventions when extending

- Keep everything in `index.html` unless there's a strong reason to split files.
- Add new tunable values as UPPER_SNAKE_CASE constants in the constants section.
- Keep update (logic) and render (drawing) concerns separate.
- Preserve the state-machine pattern when adding new screens or modes.
