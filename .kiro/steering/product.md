# Product

Breakout is a classic arcade brick-breaking game that runs entirely in the browser. The player controls a paddle at the bottom of the screen to bounce a ball upward and destroy a grid of colored bricks.

## Core gameplay

- Move the paddle with the arrow keys or the mouse; press Space to launch the ball.
- Clear all bricks to win; losing the ball three times (lives) ends the game.
- The ball speeds up gradually as more bricks are destroyed, raising difficulty over time.
- Bounce angle depends on where the ball strikes the paddle, giving the player directional control.

## State and feedback

- Game states: start screen, playing, game over, and win, each with an on-screen overlay.
- Lives are shown as hearts (top-left) and remaining bricks as a counter (top-right).
- Destroyed bricks emit a short particle burst for visual feedback.
- The game can be paused and resumed with the P key.

This is a self-contained demo/workshop project with no backend, accounts, or persistence.
