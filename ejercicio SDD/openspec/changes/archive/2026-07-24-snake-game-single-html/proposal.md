## Why

We already proved with the shake game that a self-contained single HTML file is the most portable way to distribute a small browser game (double-click to run, zero deps, works offline). We now want a second interactive game — classic Snake — following the exact same packaging rules, so the project has more than one playable demo and the single-file pattern is reused rather than reinvented.

## What Changes

- Add a new standalone `snake-game.html` at the project root, fully self-contained: one `<style>` and one `<script>`, inline SVG, no external requests.
- Implement classic Snake gameplay on a grid: directional control (keyboard + on-screen D-pad for touch), food spawning, snake growth, wall/self collision ending the round, and score tracking.
- Provide a Best-score persistence via `localStorage` (wrapped in try/catch), mirroring the shake game.
- Add a brief in-page instructions panel (dismissable / re-openable) so the file is self-explanatory when shared.
- Preserve the same packaging invariants as the shake game: works from `file://` and any static host, no build step, ≤ 200 KiB.

## Capabilities

### New Capabilities

- `snake-game-runtime`: Game loop and mechanics — grid model, snake movement, food spawning, growth, wall/self collision, score, and reset, all driven from a single HTML file.
- `snake-game-presentation`: Visual presentation layer — inline CSS, canvas/grid rendering, animations, inline SVG assets, and an instructions panel so the file is portable and offline-capable.
- `snake-game-packaging`: Single-file packaging rules — no external network requests, no build step, file opens directly via `file://` or any static host, size ≤ 200 KiB. (Mirrors the shake-game packaging capability.)

### Modified Capabilities

_None._ This introduces new capabilities only; it does not change any existing spec-level behavior.

## Impact

- **Code**: New single `snake-game.html` file at the project root. No changes to `shake-game.html` or the existing archived shake-game specs.
- **APIs**: Standard browser APIs only: `CanvasRenderingContext2D`, `addEventListener` (keyboard + pointer), `requestAnimationFrame`, and `localStorage` (optional, for high score).
- **Dependencies**: No runtime dependencies. No CDNs, build toolchain, or dev server.
- **Browser support**: Evergreen Chromium, Firefox, and Safari. Keyboard control on desktop; on-screen directional pad for touch devices. `prefers-reduced-motion` respected for non-essential animation.
- **Size budget**: Target ≤ 200 KiB (canvas-based rendering keeps it well under).
