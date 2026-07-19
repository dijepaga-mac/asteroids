# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Running the game

Open `index.html` directly in a browser, or serve it locally:

```bash
npx serve .
```

Then visit `http://localhost:3000`. There is no build step, no bundler, and no dependencies.

## Architecture

The entire game logic lives in a single file: `game.js`. It uses the HTML5 Canvas API directly with no frameworks.

**Game loop:** `requestAnimationFrame` drives a fixed `loop(ts)` function that calls `update(dt)` and `draw()` every frame. `dt` is capped at 50ms to prevent physics tunneling on tab refocus.

**State machine:** A global `state` variable (`'playing' | 'dead' | 'gameover'`) controls which branches of `update()` and `draw()` are active.

**Entity classes:** `Ship`, `Asteroid`, `Bullet`, and `Particle` each expose `update(dt)` and `draw()` methods. Entities are marked dead via a `.dead` flag and filtered out of their arrays each frame — there is no entity manager.

**Input:** Raw `keydown`/`keyup` events populate a `keys` map. The `justPressed` map (cleared on read via `pressed()`) handles one-shot actions like shooting.

**Collision:** Brute-force circle checks between all bullets×asteroids and ship×asteroids each frame. No spatial partitioning.

**Toroidal wrapping:** The `wrap(v, max)` utility keeps all entities within the 800×600 canvas by wrapping at edges.

**Asteroid splitting:** `Asteroid.split()` returns two new `Asteroid` instances one size smaller; size 1 asteroids return `[]`. Sizes and their radii, speeds, and point values are defined in the `RADII`, `SPEEDS`, and `POINTS` constant arrays indexed by size (1–3).

## Canvas dimensions

Canvas is fixed at 800×600, referenced as constants `W = 800` and `H = 600` at the top of `game.js`.
