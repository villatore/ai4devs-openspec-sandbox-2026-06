## Context

The shake game currently exists as a multi-file project (HTML, CSS, JS, optional image/font assets). That layout is fine for development but inconvenient for distribution: a recipient must clone a repo, run a dev server, or at minimum keep the asset directory next to the HTML file. The goal is to collapse everything into one self-contained `.html` artifact that runs identically when opened by double-click (`file://`) or served from any static host.

The implementation must be readable enough that a single person can review the entire game in one scroll, and lightweight enough to fit comfortably in an email attachment.

## Goals / Non-Goals

**Goals:**
- Deliver a single `.html` file with zero external requests after load.
- Preserve all gameplay behavior (states, scoring, shake detection, reset) from the original.
- Provide a graceful fallback path for devices/browsers where motion sensors are unavailable or denied.
- Keep the file under 200 KB and readable (no minification required for v1).
- Run identically from `file://` and from a static host.

**Non-Goals:**
- No build pipeline, bundler, or transpiler.
- No multi-file distribution or download of additional resources.
- No backend, leaderboard, or networked multiplayer.
- No service worker / PWA installability in v1 (can be a follow-up change).
- No TypeScript or modern-framework rewrite.

## Decisions

### Decision: Inline everything; one `<style>` and one `<script>`
- **Why**: Simplest way to satisfy the single-file constraint. One style and one script block keeps the document structure obvious and the file easy to read or diff.
- **Alternatives considered**:
  - Multiple `<style>`/`<script>` blocks per logical area — rejected: harder to read, no functional benefit.
  - External modules loaded via `blob:` URLs — rejected: complicates the file structure for no payoff.
  - SVG-only inline assets (no data URIs) — adopted for icons where natural; data URIs reserved for any bitmap that survives from the original.

### Decision: Use a tiny state machine for game states
- **Why**: `idle` → `playing` → `ended` → `idle` is small enough to model explicitly without a framework. A `state` variable plus a `setState(next)` helper that gates shake handling and the render loop is the most readable form.
- **Alternatives considered**:
  - Event-bus / pub-sub — rejected: overkill for three states.
  - Class-based OOP — rejected: introduces a `class` keyword that, while fine, is heavier than needed for ~150 LOC.

### Decision: Motion API with a feature-detect + permission gate
- **Why**: iOS Safari requires `DeviceMotionEvent.requestPermission()` inside a user gesture; other platforms fire the event directly. A single `enableMotion()` function tries permission, falls back silently, and the on-screen "Shake" button is always available as a guaranteed path.
- **Alternatives considered**:
  - `devicemotion` only — rejected: blocks iOS users behind a permission flow with no fallback.
  - Accelerometer-only via `sensor` API — rejected: not yet broadly available in Safari and adds complexity.

### Decision: `requestAnimationFrame` for the loop, not `setInterval`
- **Why**: Aligns updates with the display refresh, pauses when the tab is hidden (saves battery), and is the browser-recommended primitive.
- **Alternatives considered**: `setInterval(fn, 16)` — rejected: drifts, doesn't pause on hidden tabs, and is less smooth.

### Decision: Optional high score in `localStorage`
- **Why**: One extra line of code (`localStorage.getItem('shakeHigh')`) gives meaningful persistence without a backend. Failure to read/write is non-fatal and logged once.
- **Alternatives considered**: Cookie-based — rejected: unnecessary for a single-value preference; sessionStorage — rejected: high score would not survive a tab close.

### Decision: Mobile-first responsive layout via CSS flex/grid
- **Why**: The game is most fun on a phone; a single column with large tap targets reads well across viewports. No media query gymnastics.
- **Alternatives considered**: A dedicated desktop layout — rejected: adds CSS bulk without changing gameplay.

## Risks / Trade-offs

- **[Risk] File size creeps past 200 KB** if decorative imagery is inlined → Mitigation: prefer inline SVG for icons; convert any required bitmaps to optimized base64 with a hard cap; surface size in tasks.md.
- **[Risk] `DeviceMotionEvent` permission UX varies across browsers and can confuse users** → Mitigation: keep the on-screen "Shake" button always visible and styled as a primary action; show the permission prompt only inside the explicit "Enable motion" gesture.
- **[Risk] iOS Safari attaches `deviceorientation`/`devicemotion` only to the window object in certain conditions** → Mitigation: attach listeners on `window` and verify existence before binding; degrade silently.
- **[Risk] Opening from `file://` blocks some APIs (e.g., service workers, `localStorage` in private mode)** → Mitigation: wrap `localStorage` access in try/catch; the game remains playable without persistence.
- **[Risk] Game logic and styles become a wall of text in one file** → Mitigation: use clear section comments (`<!-- SECTION: ... -->` and `// SECTION: ...`) so the file remains scannable.

## Migration Plan

1. Build the single-file artifact at the project root as `shake-game.html`.
2. Manual smoke test: open via `file://` in Chrome, Firefox, and Safari (desktop + iOS) and verify the four gameplay states.
3. Manual network test: load the file, then disable the network, then play — confirm no errors and full functionality.
4. (Optional) Keep the original multi-file source under `legacy/` for one release before removing in a follow-up change.
5. Rollback: delete `shake-game.html`; the legacy source remains operational until removed.

## Open Questions

- Should v1 include a sound effect (inline base64 WAV ≤ 5 KB) or stay silent?
- Do we want a "best of 3 rounds" mode in v1, or save it for a follow-up change?
- Is there an existing brand palette / typography to honor, or pick neutral defaults?
