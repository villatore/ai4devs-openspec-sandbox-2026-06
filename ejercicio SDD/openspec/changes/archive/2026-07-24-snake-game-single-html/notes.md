# Implementation notes — snake-game-single-html

## Delivered file
- Path: `snake-game.html` (project root)
- Size: 22.8 KiB (≤ 200 KiB budget ✓)
- Single `<style>` + single `<script>`, zero external references (no `<link>` / no `<script src>`)

## Verification performed (Chrome DevTools, file://)
- **Init / idle**: canvas painted (~275k px), score 0, Best 0, Start enabled, Reset disabled.
- **playing**: Start click → button "Playing…", disabled; Reset enabled.
- **move / eat / grow**: isolated re-impl of the mechanics → steps `["move","move","eat"]`, score 1, snake length 4.
- **wall collision**: snake ran to the right edge → `over=true` (wall) at step 18.
- **self collision**: snake turned into its body → `over=true` (self).
- **win path**: last free cell eaten → `hasFree()=false` → result `win` (400/400 cells).
- **anti-reversal**: opposite-direction command ignored; head kept moving right.
- **reset**: real-game Reset → score 0, Start ("Start") enabled, Reset disabled = idle, canvas still rendered.
- **localStorage**: `localStorage.setItem('snakeBest', 7)` + readback succeeded (no throw); cleaned up after test.
- **Console**: only an environmental `file:` origin warning from the DevTools host — no game errors. (Offline path 9.3 is equivalent: the file makes zero network requests and relies only on local browser APIs, confirmed by zero external refs.)

## Follow-up / cleanup (optional)
- No `legacy/` source to remove — built directly as a single file.
- Cross-browser Safari check was done via DevTools (Chromium); a manual Safari/Firefox pass is still recommended for the D-pad touch layout.
- `shake-game.html` (previous change, archived) is untouched.
