## 1. Scaffold the single HTML file

- [x] 1.1 Create `snake-game.html` at the project root with the HTML5 boilerplate (`<!doctype html>`, `<html lang>`, `<meta charset>`, `<meta viewport>`, `<title>Snake Game</title>`)
- [x] 1.2 Add a single empty `<style>` block and a single empty `<script>` block in the document `<head>` and `<body>` respectively
- [x] 1.3 Add a top-level structural skeleton: header (title + help toggle), instructions panel, score/best display, a `<canvas>` board, an on-screen directional pad (Up/Left/Down/Right), Start/Reset controls, and a status line

## 2. Implement inline presentation

- [x] 2.1 Style the layout mobile-first with a square responsive `<canvas>` (max width, square aspect) and large tap targets; place the D-pad below the canvas
- [x] 2.2 Use inline SVG for the help icon so no external image is required
- [x] 2.3 Make the instructions panel dismissable from a clearly labeled control and re-openable from the help control
- [x] 2.4 Respect `prefers-reduced-motion` (disable non-essential animation such as food pulse)

## 3. Implement the board model and canvas rendering

- [x] 3.1 Define the grid (e.g., 20×20), cell size in CSS px, and a `drawBoard()`/`render()` routine that clears and paints background, snake, and food on the `<canvas>`
- [x] 3.2 Make the canvas high-DPI aware: set backing store to CSS size × `devicePixelRatio`, scale the context, and re-render on resize
- [x] 3.3 Represent state as `snake` (array of {x,y}, head first), `food` ({x,y}), `dir` ({x,y} heading), and `score`

## 4. Implement the game loop

- [x] 4.1 Implement a fixed-timestep loop driven by `requestAnimationFrame` + an accumulator; step the snake one cell per tick only while in `playing`
- [x] 4.2 Pause the accumulator on `visibilitychange` (document hidden) and resume without advancing lost time
- [x] 4.3 Implement an optional speed ramp: `tickMs = max(MIN, BASE - score * STEP)`

## 5. Implement snake mechanics

- [x] 5.1 Move the head one cell in the current heading per tick; shift the body by unshifting the new head and popping the tail (unless growing this tick)
- [x] 5.2 On eating food: grow (skip the tail pop), increment score by 1, and keep the previous tail cell occupied for that tick
- [x] 5.3 Spawn exactly one food cell on a free (non-snake) cell; if no free cell exists, transition to `ended` declaring a win
- [x] 5.4 Detect wall collision (head outside board bounds) → `ended`; detect self collision (head matches any body segment) → `ended`

## 6. Implement input (keyboard + touch)

- [x] 6.1 Listen for arrow keys and WASD; translate to a queued heading to apply on the next tick
- [x] 6.2 Reject a 180° reversal relative to the current heading (ignore the exact opposite direction)
- [x] 6.3 Keep a one-slot input queue so a quick double turn (e.g., right then up) both registers
- [x] 6.4 Wire the on-screen D-pad buttons to the same queueing path as the keyboard, so input is equivalent across devices

## 7. Implement state machine and controls

- [x] 7.1 Declare `state ∈ {'idle','playing','ended'}` with a `setState(next)` that re-renders controls and status
- [x] 7.2 Wire Start to transition `idle|ended → playing`, reset the board, snake, food, and score, and start the loop
- [x] 7.3 Wire Reset to return any state to `idle` with a fresh board and score 0 without reloading the page
- [x] 7.4 On `ended`, stop the loop and reveal an appropriate control ("Play again")

## 8. Implement Best-score persistence

- [x] 8.1 Wrap `localStorage` access in try/catch; read and display "Best: N" on load
- [x] 8.2 On round end, if the score exceeds the stored best, persist it and update the display

## 9. Verify packaging and offline behavior

- [x] 9.1 Confirm the document has zero `<link rel="stylesheet">` and zero `<script src="...">` elements
- [x] 9.2 Open via `file://` in Chrome, Firefox, and Safari and confirm start → eat → grow → collide → reset (and a win via a full board) work with no console errors
- [x] 9.3 Load the page, disable the network, play a full round, and confirm no broken functionality
- [x] 9.4 Verify the file size is ≤ 200 KiB; if over, trim decorations (canvas needs no image assets)
- [x] 9.5 Document the final file path and any follow-up cleanup in the change folder's `notes.md` (optional)
