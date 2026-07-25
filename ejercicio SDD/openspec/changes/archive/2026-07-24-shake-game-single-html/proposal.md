## Why

The existing shake game logic is currently scattered across multiple files (HTML, CSS, JS, and assets), which makes it cumbersome to share, demo, and run offline. We want to package the entire experience into a single self-contained HTML file so it can be opened with a double-click, emailed, hosted as a static asset, or pasted into a browser without any build step or local server.

## What Changes

- Package the entire shake game (markup, styles, scripts, and inline data) into one standalone `.html` file.
- Inline all CSS inside a `<style>` block and all JavaScript inside a `<script>` block; remove external `src`/`href` references.
- Encode any images or fonts as data URIs so the file works fully offline.
- Preserve all gameplay behavior (shake detection, scoring, win/lose conditions, reset) and visual fidelity from the original.
- Add a brief in-page instructions panel so the game is self-explanatory when shared standalone.

## Capabilities

### New Capabilities

- `shake-game-runtime`: Browser runtime for the shake game — game loop, input handling (pointer + DeviceMotion / DeviceOrientation), score state, and reset semantics, all running from a single HTML file.
- `shake-game-presentation`: Visual presentation layer — inline CSS, layout, animation, and inline data-URI assets so the file is portable and offline-capable.
- `shake-game-packaging`: Single-file packaging rules — no external network requests, no build step, file opens directly via `file://` or any static host.

### Modified Capabilities

_None._ This change introduces new capabilities only; it does not modify existing spec-level behavior (the original game had no formal spec).

## Impact

- **Code**: New single `shake-game.html` file at the project root (or `dist/` if a build output dir exists). Original multi-file source can be retained for reference or removed in a follow-up.
- **APIs**: No new public APIs. Uses standard browser APIs: `DeviceMotionEvent`, `DeviceOrientationEvent`, `requestAnimationFrame`, and `localStorage` (optional, for high score).
- **Dependencies**: No runtime dependencies. Removes the need for any CDNs, build toolchain, or local dev server.
- **Browser support**: Targets evergreen Chromium, Firefox, and Safari. Mobile Safari requires user permission prompt for motion sensors (graceful fallback to on-screen "Shake" button).
- **Size budget**: Target file size under 200 KB to remain trivially shareable.
