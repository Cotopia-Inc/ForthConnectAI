README.md

# Forth Connect — Connect‑4 Template

Version: 1.0  
Last updated: 2025

A lightweight, single-file Connect‑4 game built with HTML5 Canvas and vanilla JavaScript. Responsive, touch-friendly, keyboard-accessible, and easy to rebrand or embed. Ideal as a standalone demo, website widget, or sellable template.

Contents
- `index.html` — complete single-file app (HTML + CSS + JS)
- `images/` — default logos and artwork
- `README.md` — this file
- `GAME_USER_GUIDE.md` — full user guide (optional, include if distributing)
- `LICENSE` — license (see LICENSE)
- `demo/` (optional) — screenshots / GIFs
- `build/` (optional) — minified production bundle

Quick start (open and play)
1. Unzip the package into a folder.
2. Open `index.html` in a modern browser (Chrome, Firefox, Edge, Safari).
3. Play:
   - Mouse/touch: hover/tap a column to highlight; click/tap to drop.
   - Keyboard: Left/Right arrows to move highlight; Enter/Space to drop.
   - Restart with the Restart button or click after a finished game.

Recommended local server (optional, for consistent behavior)
- Python 3:
  - `python -m http.server 8000`
  - open `http://localhost:8000`
- Node (http-server):
  - `npm install -g http-server`
  - `http-server -p 8080` → `http://localhost:8080`

Files to edit for quick branding
- `index.html` — replace header/footer logos, edit text, tweak colors and constants.
- CSS root variables (inside `index.html` near top of `<style>`) for color theming:
  - `--bg`, `--board-frame`, `--board-bottom`, `--player`, `--player-dark`, `--computer`, `--computer-dark`, `--tie`, `--tie-dark`, `--win-ring`
- JS constants (inside `index.html` script):
  - `GRID_COLS`, `GRID_ROWS`, `DELAY_COMP`, `GRID_CIRCLE`, `MARGIN`

Customization pointers
- Change logo images: replace files in `images/` and update `<img src="...">`.
- Change theme: edit CSS variables.
- Change board size: update `GRID_COLS` / `GRID_ROWS` and validate UI.
- Make AI stronger: replace `aiThink()` with a minimax implementation (see Developer Notes).
- Convert to framework: extract script into module and integrate with React/Vue lifecycle.

Accessibility
- Canvas has `aria-label`.
- Turn indicator and status areas use `aria-live`.
- Keyboard support: Left/Right + Enter/Space.
- For advanced accessibility (recommended), see `ACCESSIBILITY_DEVELOPER_GUIDE.md` (included below or appended).

Testing checklist
- Mouse/touch/keyboard moves work.
- AI moves after a short delay and highlights selection before playing.
- Win detection (horizontal, vertical, diagonal) works and winning discs highlight.
- Tie detection when board is full.
- Responsive scaling across common viewports (mobile/tablet/desktop).
- Screen reader announces turn and status updates.

Deployment
- Static hosting recommended: GitHub Pages, Netlify, Vercel, S3 + CloudFront.
- For production, extract JS into a separate file and minify (Terser / bundler) if desired.

License & distribution
- Include a clear `LICENSE` with your distribution. Decide standard/extended licensing for commercial resale.
- Provide a support policy and changelog for customers.

Support
- Include a contact address and support policy in your distribution (e.g., 30 days bug-fix support).

---

Accessibility — Developer Guide (expanded)

Purpose
This guide gives concrete steps, code examples, and best practices to make the Connect‑4 template accessible to keyboard users, screen reader users, and people with color-vision or motor access needs. It explains how to add a textual board summary, improve announcements, provide alternate color themes, and tests to verify accessibility.

1) Accessibility goals
- Ensure keyboard-only players can fully play the game.
- Provide screen-reader users with meaningful, up-to-date state information (turns, moves, results).
- Offer alternatives to color to convey important states.
- Make interactions reliable on touch and low-motor-control devices.

2) Current built-in accessibility (what’s already present)
- `canvas` element has `role="img"` and an `aria-label`.
- Turn indicator (`#turn`) and HUD/status (`#message`) use `aria-live` to announce dynamic changes.
- Keyboard controls (Left/Right, Enter/Space) allow play without a pointer.
- Focus outlines visible for interactive controls (Restart, nav items).

3) Recommended improvements (priority order)
A. Add a textual board representation (strongly recommended)
- Why: Screen readers cannot parse canvas pixels; a live textual depiction allows blind users to understand board state and plan moves.
- Implementation overview:
  - Create a visually hidden element that contains a row-by-row textual summary or an accessible table.
  - Update it after every move and ensure it is announced (use `aria-live="polite"` or `assertive` as appropriate).
- Example format (row-major, top to bottom):
  - "Board row 1: empty, empty, red, yellow, empty, empty, empty."
  - Or compressed: "R1: . . R Y . . ."
- Implementation snippet (minimal):
  - Add HTML:
    <div id="srBoard" class="sr-only" aria-live="polite"></div>
  - Add CSS:
    .sr-only { position: absolute; left: -9999px; width: 1px; height: 1px; overflow: hidden; }
  - Update on move:
    function updateScreenReaderBoard() {
      const lines = [];
      for (let r = 0; r < GRID_ROWS; r++) {
        const cells = [];
        for (let c = 0; c < GRID_COLS; c++) {
          const owner = grid[r][c].owner;
          cells.push(owner == null ? 'empty' : (owner ? 'yellow' : 'red'));
        }
        lines.push('Row ' + (r + 1) + ': ' + cells.join(', '));
      }
      document.getElementById('srBoard').textContent = lines.join('. ');
    }
  - Call `updateScreenReaderBoard()` after every placement, and on reset.
- Additional tip: consider giving column indices (1–7) as part of the summary so keyboard users can reference columns verbally.

B. Provide per-move announcements (explicit)
- What to announce:
  - Whose turn it is.
  - Which column a disc was dropped into and which row it landed in.
  - Win or draw results and which four cells completed the line.
- Example phrasing:
  - "Player moved: column 4, row 6. Computer's turn."
  - "Computer moved: column 2, row 3. Player's turn."
  - "Player wins with four in a row: column 2 row 3, column 3 row 4, column 4 row 5, column 5 row 6."
- Implementation:
  - Expand your `#message` or `#turn` aria-live updates to include these details.
  - Keep announcements concise but specific.

C. Provide an accessible control panel
- Offer explicit buttons:
  - "Move left", "Move right", "Drop disc", "Restart".
- These buttons make the game usable by assistive tech users who prefer not to use arrow keys.
- Buttons should be focusable and clearly labeled.

D. High-contrast and color-blind-friendly themes
- Provide at least one alternate palette with high contrast and non-color cues (patterns or symbols).
- Implementation strategies:
  - CSS variables toggle (data-theme attribute on `body`).
  - In addition to color, render a symbol or letter inside discs when the canvas draws (e.g., 'P' for player, 'C' for computer) to help color-blind users.
- Canvas rendering example:
  - After drawing the circle, draw a small contrasting letter at the center:
    ctx.fillStyle = (owner ? 'black' : 'white');
    ctx.font = `${Math.floor(cell.h * 0.35)}px sans-serif`;
    ctx.textAlign = 'center';
    ctx.textBaseline = 'middle';
    ctx.fillText(owner ? 'P' : 'C', cell.cx, cell.cy);
  - Make this optional via a toggle in the UI.

E. Reduce motion and timing controls
- Offer toggles to reduce or disable AI animation/delay (DELAY_COMP) and to disable other motion.
- Implementation:
  - Add a setting in UI (checkbox) and respect it when setting `timeComp` or doing visual highlights.

F. Touch & pointer improvements
- Increase target area for touch:
  - Add invisible vertical regions aligned to columns to make taps more forgiving.
- Debounce taps to avoid accidental double placements on some devices.

4) Example: Full accessibility integration checklist and code hooks
- Add the following HTML near the HUD:
  <div id="srBoard" class="sr-only" aria-live="polite"></div>
  <div class="a11y-controls">
    <button id="moveLeft">Move left</button>
    <button id="moveRight">Move right</button>
    <button id="dropDisc">Drop disc</button>
    <label><input type="checkbox" id="highContrast"> High contrast</label>
    <label><input type="checkbox" id="reducedMotion"> Reduce motion</label>
  </div>
- JavaScript hooks:
  - On any state update (move, restart, AI move), call:
    - updateScreenReaderBoard();
    - announceMove({ actor, column, row, result });
  - Wire `moveLeft`, `moveRight`, `dropDisc` buttons to the same keyboard handlers.
  - Implement `toggleHighContrast()` to switch CSS variables or set a `data-theme` attribute on the `body` and re-render canvas.
  - Implement `reducedMotion` to set `DELAY_COMP` to 0 when enabled.

5) Screen reader phrasing guidance (best practices)
- Use short sentences and consistent wording.
- Prefer the active voice: "Player moved — column 4, row 6."
- Avoid technical terms; use player/computer or human/AI.
- Read critical events with higher priority (aria-live="assertive") if they require immediate attention (winning move). Use "polite" for routine turn updates.

6) Keyboard navigation recommendations
- Ensure focus order is logical and predictable:
  - Nav → controls (Move left/right, Drop, Restart) → canvas area (if focusable) → footer.
- If the canvas is focusable (optional), handle Enter/Space to act like Drop.
- Keep visible focus ring styling and ensure it contrasts with background.

7) Testing guidance
- Screen reader testing:
  - NVDA (Windows) + Firefox
  - VoiceOver (macOS) + Safari
  - TalkBack (Android) with Chrome for mobile testing
  - Verify aria-live messages announce turn changes and results.
- Keyboard testing:
  - Tab through interactive controls and verify actions with Enter/Space.
  - Use only keyboard to play a full game and verify parity with pointer play.
- Contrast & visual:
  - Use tools (axe, Lighthouse, WCAG contrast analyzers) to verify color contrast of HUD and focus outlines.
- Real-world user testing:
  - If possible, have at least one screen reader user and one keyboard-only user try the UI and provide feedback.

8) Accessibility conformance targets
- Aim for WCAG 2.1 AA compliance for essential UI elements:
  - Text contrast for important UI text (>= 4.5:1 where applicable).
  - Keyboard operability for all functionality.
  - Informative ARIA attributes and live regions for dynamic data.

9) Example code: screen-reader board & announcements (concise)
- Add sr-only container:
  <div id="srBoard" class="sr-only" aria-live="polite"></div>
- JS (call after any state change):
  function updateScreenReaderBoard(){
    const rows = [];
    for (let r = 0; r < GRID_ROWS; r++){
      const parts = [];
      for (let c = 0; c < GRID_COLS; c++){
        const owner = grid[r][c].owner;
        parts.push(owner == null ? 'empty' : (owner ? 'yellow' : 'red'));
      }
      rows.push('Row ' + (r + 1) + ': ' + parts.join(', '));
    }
    document.getElementById('srBoard').textContent = rows.join('. ');
  }
  function announceMove(actor, col, row, extra){
    const text = actor + ' moved: column ' + (col + 1) + ', row ' + (row + 1) + (extra ? ('. ' + extra) : '');
    document.getElementById('message').textContent = text;
  }

10) Final accessibility notes
- Accessibility is iterative: implement the textual board and per-move announcements first, then refine UI affordances (buttons, contrast, reduced motion), and finally run assistive-technology tests.
- Provide documentation in your product describing the keyboard shortcuts and accessible controls.
- Consider adding a small accessibility settings panel so site owners can enable/disable features to meet their audience needs.