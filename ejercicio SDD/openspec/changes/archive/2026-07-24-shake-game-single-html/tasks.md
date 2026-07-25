## 1. Scaffold the single HTML file

- [x] 1.1 Create `shake-game.html` at the project root with the HTML5 boilerplate (`<!doctype html>`, `<html lang>`, `<meta charset>`, `<meta viewport>`, `<title>Shake Game</title>`)
- [x] 1.2 Add a single empty `<style>` block and a single empty `<script>` block in the document `<head>` and `<body>` respectively
- [x] 1.3 Add a top-level structural skeleton: header (title + help toggle), instructions panel, score/timer display, action area (Start, Shake, Reset), and a status line

## 2. Implement inline presentation

- [x] 2.1 Style the layout with mobile-first CSS (flex/grid), large tap targets, and a single-column flow
- [x] 2.2 Add visual feedback for shake events (a brief scale/opacity pulse on the score or a dedicated "shake" indicator)
- [x] 2.3 Use inline SVG for any icons (help, reset) so no external image is required
- [x] 2.4 Make the instructions panel dismissable from a clearly labeled control and re-openable from the help control

## 3. Implement the game state machine

- [x] 3.1 Declare a `state` variable with one of `'idle' | 'playing' | 'ended'` and a `setState(next)` helper that updates it and re-renders the affected DOM
- [x] 3.2 Wire the Start control to transition `idle → playing`, reset the score, and start a `requestAnimationFrame` loop
- [x] 3.3 Implement a 30-second (configurable) round timer inside the loop that transitions `playing → ended` on expiry
- [x] 3.4 Wire the Reset control to transition any state back to `idle` with score 0 and stop the loop
- [x] 3.5 Gate all shake handling so shakes in `idle` or `ended` are ignored

## 4. Implement shake detection with fallback

- [x] 4.1 Add an `enableMotion()` function that feature-detects `DeviceMotionEvent` and, on iOS, calls `requestPermission()` inside a user gesture
- [x] 4.2 Bind a `devicemotion` listener that computes linear-acceleration magnitude and registers a shake when the threshold is exceeded for two consecutive samples in a 200 ms window
- [x] 4.3 Keep the on-screen "Shake" button always visible and wired to register exactly one shake per activation
- [x] 4.4 If motion is denied or unavailable, log once to the status line and continue with the button-only path

## 5. Implement scoring and persistence

- [x] 5.1 Increment the score by 1 on each registered shake in `playing` and reflect it in the DOM within the next animation frame
- [x] 5.2 Wrap `localStorage` access in try/catch; on success, persist and display a "Best" value
- [x] 5.3 When the round ends, compare the current score to the stored best and update if higher

## 6. Verify packaging and offline behavior

- [x] 6.1 Confirm the document has zero `<link rel="stylesheet">` and zero `<script src="...">` elements
- [ ] 6.2 Open the file via `file://` in Chrome, Firefox, and Safari and confirm all four states work end-to-end with no console errors
- [ ] 6.3 Load the page, then disable the network, then play a full round and confirm no broken functionality
- [ ] 6.4 Verify the file size is ≤ 200 KiB; if over, replace remaining bitmaps with SVG or trim decorations
- [ ] 6.5 Document the final file path and any follow-up cleanup in the change folder's `notes.md` (optional)
