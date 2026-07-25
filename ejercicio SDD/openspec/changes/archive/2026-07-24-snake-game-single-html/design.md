## Context

This change adds a second single-file browser game (classic Snake) to the project using the same packaging pattern proven by the shake game. The shake game established that a self-contained `.html` (one `<style>`, one `<script>`, inline SVG, no network) is the most portable way to distribute a small game. Snake is a natural second demo: it needs graphical rendering (favoring a `<canvas>` over DOM), keyboard + touch input, and a short game loop — all within the same ≤ 200 KiB single-file constraint.

## Goals / Non-Goals

**Goals:**
- Deliver a single `snake-game.html` with zero external requests after load, mirroring the shake-game packaging invariants.
- Implement classic Snake: grid model, queued directional input, growth on food, wall/self collision, win on full board, reset.
- Support both keyboard (arrows / WASD) and an on-screen D-pad for touch, treated equivalently.
- Persist a "Best" score via `localStorage` (safe try/catch), consistent with the shake game.
- Run identically from `file://` and any static host; respect `prefers-reduced-motion`.

**Non-Goals:**
- No build pipeline, bundler, or transpiler.
- No backend, multiplayer, or shared leaderboards.
- No PWA / service worker in v1.
- No difficulty settings in v1 (a single, fixed starting speed is fine; speed may ramp as the snake grows — a small enhancement, driven by the same loop).
- No modification to the shake game or its archived specs.

## Decisions

### Decision: Render on a single `<canvas>`, not DOM cells
- **Why**: A grid of 20×20 DOM elements is wasteful and slow to repaint; canvas draws the board in one pass each tick and keeps the file tiny (no cell DOM/CSS). One `<canvas>` also trivially satisfies "inline assets / no external media".
- **Alternatives considered**:
  - CSS grid of `<div>` — rejected: heavier DOM, more CSS, repainting is more work.
  - Inline SVG with rects — rejected: workable but per-frame DOM updates are costlier than canvas and the file grows.

### Decision: Fixed-timestep loop with accumulator, paused on hidden tab
- **Why**: Snake moves whole cells per tick, so it is naturally discrete. A fixed timestep (e.g., 150 ms/tick, optionally accelerating) decoupled from `requestAnimationFrame` keeps gameplay deterministic; the rAF callback fires as often as the display refresh but only steps the simulation when the accumulator crosses one tick. Pausing on `visibilitychange` (and not advancing the accumulator while hidden) prevents huge catch-up jumps.
- **Alternatives considered**:
  - `setInterval` — rejected: drifts, doesn't pause on hidden tab, not smooth.
  - Sub-cell interpolation (movement between cells) — rejected: adds complexity for little benefit in a cell-based grid.

### Decision: Queued input, reject 180° reversals
- **Why**: Reading the key directly would let the player reverse into the snake's neck mid-tick. Instead, on input we compute the next heading *relative to the current heading* and ignore the exact opposite; the change takes effect on the next tick. Keeping a one-slot input queue lets a quick double-tap (e.g., right then up) both register without dropping the turn.
- **Alternatives considered**:
  - Apply heading instantly — rejected: allows illegal reversals and mid-cell turns.
  - Multi-slot input buffer — rejected: overkill for a one-cell-per-tick game; a single queued turn is enough.

### Decision: Board state as arrays: `snake` (list of {x,y}), `food` {x,y}, `dir`
- **Why**: Minimal, easy to reason about. The head is `snake[0]`; growth means not popping the tail that tick; collision is a bounds check plus a check of the head against the body (excluding the tail when not growing). Free-cell food placement picks a random cell not in the snake set; if no free cell exists, the round is won.
- **Alternatives considered**:
  - 2D occupancy grid — rejected: fine but redundant given the small N; arrays are simpler to render.
  - Linked list — rejected: unnecessary in JS.

### Decision: Optional speed ramp tied to score
- **Why**: Slightly increases challenge as the snake grows with no extra input or config — derive `tickMs = max(MIN, BASE - score * STEP)`. Keeps gameplay from plateauing; "non-essential" so `prefers-reduced-motion` can disable it.
- **Alternatives considered**:
  - Constant speed — rejected: gets dull once the player is practiced.
  - Explicit levels — rejected: out of scope for v1.

### Decision: Safe `localStorage` Best score
- **Why**: One value, `localStorage.getItem('snakeBest')`, wrapped in try/catch (covers `file://` quirks, private mode, disabled storage). Identical approach to the shake game for consistency.
- **Alternatives considered**: sessionStorage — rejected: best score shouldn't reset per tab.

## Risks / Trade-offs

- **[Risk] Input feels laggy due to fixed timestep** → Mitigation: short base tick (e.g., 140 ms) and a one-slot input queue so fast turns register on the next tick.
- **[Risk] `file://` blocks `localStorage` in some browsers / private mode** → Mitigation: wrap all storage in try/catch; game stays fully playable without persistence.
- **[Risk] Canvas blurry on high-DPI screens** → Mitigation: set `canvas.width/height` to the CSS pixel size × `devicePixelRatio` and scale the context; re-do on resize.
- **[Risk] Touch D-pad overlaps the board on small screens** → Mitigation: board uses available width with a max size; D-pad sits below the canvas, not over it.
- **[Risk] File size creeps from decorations** → Mitigation: inline SVG for the few icons; no external fonts (system stack); canvas needs no image assets — well under 200 KiB expected.

## Migration Plan

1. Build `snake-game.html` at the project root.
2. Manual smoke test: play via keyboard and via on-screen D-pad, verify start/eat/grow/collide/reset/win paths, check `localStorage` persists Best across reloads.
3. Offline test: load the file, disable network, play a full round.
4. Cross-browser spot check: Chrome, Firefox, Safari (desktop) for layout and input.
5. No rollback complexity: deleting `snake-game.html` removes the change; the shake game is untouched.

## Open Questions

- Should speed ramp up with score in v1, or stay constant? (Design assumes a gentle ramp.)
- Starting board size — 20×20 (classic) is assumed; confirm or adjust.
- Should the on-screen D-pad also support swipe gestures on the board for a more native feel? (Not in v1 scope.)
