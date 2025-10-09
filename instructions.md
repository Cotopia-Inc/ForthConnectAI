Forth Connect — Game User Guide

Version: 1.0
Last updated: Oct. 2025

Table of contents
- Overview
- Intended audience
- System requirements
- Files included in the template
- Installation and first run (step-by-step)
- User interface overview
- Controls and input methods (mouse, touch, keyboard)
- Gameplay and rules (detailed)
- Computer opponent (AI) behavior and timing
- Game flow examples (sample turns)
- Accessibility features and recommendations
- Customization & branding (detailed how-to)
- Integration & developer notes
- Deployment options and best practices
- Testing checklist
- Troubleshooting (common issues & fixes)
- Licensing, packaging and distribution considerations
- Frequently asked questions (FAQ)
- Support & contact

---

Overview
This guide explains how to load, run, play, customize and ship the Forth Connect Connect‑4 template. It is written for both end users (players) and developers who will integrate, rebrand, or sell the template. It covers hands‑on usage, technical setup, design decisions, customization points, accessibility, and practical tips for distributing the template to customers.

Intended audience
- End users who want to play the game locally in a browser.
- Template buyers who will rebrand, edit, embed, or resell the product.
- Developers who will integrate the canvas game into a website or convert to a framework (React/Vue) component.

System requirements
- Desktop or mobile device with a modern browser:
  - Chrome (latest), Firefox (latest), Edge (Chromium), Safari (mobile/desktop).
- No web server required for basic testing (file:// works), but serving via HTTP is recommended for consistent behavior.
- Optional: Node.js and npm if you want to build/minify or integrate into a JS toolchain.

Files included in the template
- index.html — the complete single-file app (HTML + CSS + JS).
- images/ — default logo(s) and artwork used by the page.
- README.md — short quick-start (you can replace with this Game User Guide).
- LICENSE — license terms (add your commercial terms).
- optional demo/ — GIF or screenshot used in marketing.
- optional build/ — minified distribution (if you provide it).

Installation and first run (step-by-step)
1. Unpack the template
   - Extract the ZIP archive to a folder on your computer (e.g., ForthConnect/).

2. Open the game in a browser (basic)
   - Option A — Double-click:
     - Locate index.html and double-click to open. The file will open in your default browser.
   - Option B — File → Open:
     - Open your browser, choose File → Open File → select index.html.
   - Note: Some browsers restrict certain features on file:// protocol; if you run into odd behavior, use option 3.

3. Recommended: serve the folder over HTTP
   - Serving over HTTP avoids file:// inconsistencies (e.g., on some mobile browsers or when fetching external assets).
   - Using Python:
     - macOS / Linux / Windows (with Python installed)
       - Open a terminal at the template folder and run:
         - python -m http.server 8000
       - In your browser open: http://localhost:8000
   - Using Node (http-server):
     - npm install -g http-server
     - http-server -p 8080
     - Open http://localhost:8080
   - Using VS Code Live Server: right-click index.html → Open with Live Server.

4. Verify the board loads
   - The page will show a header with logo/title, the board canvas in the center, HUD (status), and a footer.
   - If the canvas is blank or clipped:
     - Resize the browser window (the canvas is responsive and will refit).
     - If still blank, open the browser console (F12) and check for errors.

User interface overview
- Header
  - Left: logo and game title.
  - Middle/Right: navigation links and a small dropdown with Instructions / Terms / Privacy (editable).
  - Controls area: Turn indicator (shows whose turn it is) and Restart button.
- Main area
  - Canvas: where the board, discs, highlights and victory/ draw messages are displayed.
  - HUD: textual status updates for gameplay and accessibility (aria-live).
- Footer
  - Branding, copyright and optional links.

Controls and input methods
The game supports mouse, touch, and keyboard controls so it is playable on desktop and mobile.

Mouse
- Hover: moving the mouse across the canvas highlights the top of the column where a disc would drop; the highlight shows the first available cell from the bottom in that column.
- Click: clicking anywhere in the highlighted column drops a disc into the lowest available cell. If a click occurs after the game ends, the game will restart.

Touch
- Touch and drag: touching and sliding over the canvas behaves like hover (the column under the finger is highlighted).
- Tap: tapping on a column drops a disc into the highlighted spot.
- Note: touchmove uses passive listeners for performance. On some devices, tap precision may vary; ensure test on iOS and Android.

Keyboard
- Left / Right Arrow:
  - Moves a persistent keyboard highlight left or right across columns (useful for keyboard-only players or accessibility testing).
- Enter or Space:
  - Place the disc in the currently highlighted column (or restart if the game has ended).
- Tab:
  - Standard tab order will land on interactive controls (nav links / dropdown / Restart), and focus outlines are visible.
- Screen reader:
  - The turn indicator and status messages use aria-live to announce updates.

Gameplay and rules (detailed)
Overview
Forth Connect is a two-player vertical Connect‑Four game. You play as the Player (yellow) and the computer is the opponent (red). The objective is to align 4 discs of your color in a horizontal, vertical, or diagonal line before your opponent does.

Board layout
- Columns: 7
- Rows: 6
- Cells: Each cell can be empty, held by the Player, or held by the Computer.

Turn order
- At start of a new game, the first player is randomly chosen (Player or Computer).
- The turn indicator shows "Turn: Player" or "Turn: Computer".
- On Player's turn:
  - Use mouse/touch/keyboard to select a column and drop a disc.
- On Computer's turn:
  - The AI analyses the board and selects a column. A small delay (DELAY_COMP) occurs to make the move feel natural.

Placing a disc
- When you click/tap or press Enter/Space while a column is highlighted:
  - The disc occupies the lowest open cell in that column.
  - The game checks for a win or tie.
  - If no win/tie, the turn switches to the opponent.

Winning
- The game checks for four-in-a-row in four directions:
  - Horizontal (left-right)
  - Vertical (up-down)
  - Diagonal (top-left to bottom-right)
  - Diagonal (top-right to bottom-left)
- When a line of 4 for a player is detected:
  - The four winning cells are marked visually with a darker ring or stroke.
  - The game sets gameOver = true and displays a victory message on the canvas.
- Tie (Draw):
  - If all cells are filled and no four-in-a-row exists, the game is a draw; draw message displayed.

Computer opponent (AI) behavior and timing
AI purpose
- The included AI is rule-based, designed to give a reasonable challenge without being computationally intensive. It prioritizes:
  1. Winning moves (if the AI can win immediately).
  2. Blocking moves (prevent Player from winning on next move).
  3. Neutral safe moves.
  4. Avoiding moves that immediately allow the Player to win (least desirable).

AI internals (summary)
- For each column:
  - Identify the first available cell (lowest empty slot).
  - Temporarily simulate placing a disc and test:
    - If AI placing there would produce a win → mark as winning option.
    - Else if Player placing there would produce a win → mark as blocking option.
    - Else simulated next-row checks determine whether the move gives the Player a forced win next turn.
  - Options are bucketed and a random choice is picked from the highest-priority non-empty bucket.
- Timing:
  - After the AI chooses a column, a short time delay (DELAY_COMP) is set. During this delay the selected column is highlighted; when the timer expires the AI executes the move. This gives a natural feel and time for visual anticipation.

Game flow examples
Example 1: Player starts and wins
- Player clicks column 4 → disc drops to bottom of col 4.
- Computer thinks and chooses to block elsewhere.
- After several moves, Player aligns four horizontally:
  - The four cells get winner rings and the canvas displays "Player WINS!"
  - HUD explains how to restart.

Example 2: Computer forced to block
- Player has three in a row and is poised to win next move.
- Computer detects the imminent win and places a disc in the required column to block.
- Play continues.

Accessibility features and recommendations
Built-in features
- aria-label on the canvas describing the board.
- aria-live regions for turn and status messages to announce turn changes and results to assistive technologies.
- Keyboard controls (Left/Right, Enter/Space) for players who cannot use a mouse or touch.
- Visible focus outlines for interactive elements to support keyboard navigation.

Recommended accessibility improvements (for buyers)
- Provide a textual board summary as an optional panel (one-line per row), for screen readers that cannot parse the canvas:
  - Example format: "Row 6: ., Y, R, ., ., ., ." where Y = Player, R = Computer, . = empty.
  - Update this summary on every move and expose with aria-live.
- High-contrast theme: ensure color variables meet WCAG contrast ratios. Offer a color-toggle for a color-blind friendly palette.
- Allow users to configure or disable animations/delays (for vestibular sensitivity).

Customization & branding (detailed how-to)
Colors & theme
- The CSS uses :root variables for quick color edits. Open index.html and edit the variables near the top:
  - --bg (background)
  - --board-frame, --board-bottom
  - --player, --player-dark
  - --computer, --computer-dark
  - --tie, --tie-dark, --win-ring
- Changes will immediately reflect in the rendered palette.

Replace logos and images
- Store new images in the images/ directory; update the <img> src attributes in header/footer to point to your image names.
- Ensure images are optimized (webp or png) and sized appropriately for retina devices (2x if needed).

Change board dimensions (advanced)
- Constants are declared in the JS:
  - GRID_COLS (default 7)
  - GRID_ROWS (default 6)
- Caveats:
  - The game code expects a "connect 4" win condition (four in a row). If you change grid dimensions, the win condition still checks for 4 in a row; update logic if you want 5-in-a-row.
  - Visual spacing and typography scale automatically, but extremely small or huge dimensions may need additional CSS tweaks.

Modify AI difficulty (developer tips)
- Simple adjustments:
  - To make AI slower/faster: change DELAY_COMP (seconds).
  - To make AI weaker: add randomness or bias towards neutral moves more often.
- Advanced: Replace the rule-based AI with a minimax search (with heuristics), alpha-beta pruning, or Monte Carlo tree search for stronger play. This requires:
  - A copy/clone of the game state for simulation (not mutating the current board).
  - A scoring heuristic that rates non-terminal board positions.
  - Depth limit and pruning to control performance.

UI and copy editing
- Text labels:
  - Change TEXT_PLAY, TEXT_COMP, and HUD strings in the JS to match your brand voice or language.
- Add multi-language support:
  - Extract user-facing strings into an object keyed by locale and load the appropriate set on page load.

Integration and developer notes
Embedding in an existing site
- Two approaches:
  1. Inline: copy the canvas container and script into your page and ensure IDs/class names don’t conflict.
  2. Iframe: put index.html in its folder and embed as an iframe to keep the game sandboxed and avoid CSS/JS collisions.
- If using frameworks:
  - React: move the game logic into a component, create a ref to the canvas, and call resize/createGrid/render inside useEffect hooks. Keep state in refs or component state carefully to avoid double rendering.
  - Vue: similar use of refs and lifecycle hooks.

Refactoring tips
- To support bundlers, extract JS into a module (script.js), and CSS into style.css.
- Use strict separation between UI and game logic to enable unit testing:
  - Keep board state and win detection in pure functions that accept and return states rather than mutating DOM.

Deployment options and best practices
Static hosting (recommended)
- GitHub Pages:
  - Push the repo with index.html to a gh-pages branch or enable Pages from the repo settings.
- Netlify:
  - Drag-and-drop the folder onto Netlify for instant hosting, or connect a Git repo.
- Vercel:
  - Add project and select the folder; Vercel will deploy static files.

Performance & caching
- Optimize images and strip unnecessary metadata.
- Set caching headers when hosting to reduce bandwidth for returning users.
- Consider minifying the JS for production (extract to script.js and run terser, or use a bundler).

Security considerations
- The game is purely client-side; minimal security concerns.
- If integrating multiplayer or server-side features later, secure WebSocket endpoints and authenticate users as appropriate.

Testing checklist
Before delivering the template to customers, confirm:
- Functional tests:
  - Player can highlight and place discs by mouse, keyboard and touch.
  - Computer moves occur with delay and are visualized.
  - Win detection works for horizontal, vertical, both diagonals.
  - Tie detection triggers when board is full.
  - Restart button starts a new game and resets board.
- Visual tests:
  - Board scales correctly across common viewport sizes (mobile, tablet, desktop).
  - Winning rings and highlight colors are visible and not clipped.
- Cross-browser:
  - Test on Chrome, Firefox, Edge and Safari (desktop and mobile).
- Accessibility:
  - Test keyboard-only play and screen reader announcements for aria-live regions.
- Performance:
  - Verify animation is smooth and CPU usage is reasonable on low-end devices.

Troubleshooting (common issues and fixes)
- Canvas appears blurry
  - Ensure devicePixelRatio scaling is enabled: canvas.width/height should be set to CSS size × devicePixelRatio, and ctx.setTransform used so drawings scale correctly.
- Clicks or highlights off by an offset
  - Confirm clientToCanvas() uses getBoundingClientRect() and subtracts rect.left/top to convert client coordinates to canvas CSS pixels.
- AI moves immediately or repeatedly
  - Ensure the playersTurn flag toggles correctly only after a successful placement; AI runs only when playersTurn is false.
- Touch events not triggering
  - Verify touch listeners (touchmove/touchstart) exist and use the first touch point; some mobile browsers need pointer events.
- Game freezes after resize
  - Recalculate grid and re-render on window resize (debounce optional). The template includes a resize handler to rebuild grid geometry.

Licensing, packaging and distribution considerations
- Provide a clear license:
  - Personal / commercial / extended with defined usage rights.
  - Sample: “Standard License — use in one end product, not for resale” vs. “Extended License — redistribution permitted”.
- Package the template:
  - Include index.html, images/, README.md (short), Game User Guide (this document), LICENSE, and optionally demo/ and build/.
- Consider including:
  - A minified JS build and a dev copy comment-annotated for easy editing.
  - A “how to credit” small file or badge if you expect attribution.

Frequently Asked Questions (FAQ)
Q: Can I change the win condition to 5-in-a-row?
A: Yes — change the win detection logic (connect4/count threshold) to 5 and verify grid sizing and behavior. Note: gameplay balance changes.

Q: Can I add online multiplayer?
A: Yes — this template is client-only. For online play, implement a server (WebSocket) to synchronize moves and maintain authoritative state. Use the existing placeHighlighted() and state transitions as a guide.

Q: How do I make the AI unbeatable?
A: Implement a minimax algorithm with alpha-beta pruning. For Connect‑4 this is feasible, but code complexity increases. For production, cap search depth and use heuristic evaluation to balance strength and performance.

Q: How do I translate the UI to another language?
A: Extract all text into a strings object keyed by language code. Load the appropriate labels at initialization and update UI elements accordingly.

Support & contact
- If you ship the template, include a small support policy (e.g., 30 days free support for bug fixes).
- Provide an email address or a support page for buyer questions.
- Keep a changelog and versioning in your distribution so buyers can track updates.

Appendix A — Developer hooks & editable variables (quick reference)
- JS constants:
  - DELAY_COMP — seconds for AI move delay
  - GRID_COLS — number of columns
  - GRID_ROWS — number of rows
  - GRID_CIRCLE — disc size fraction
  - MARGIN — outer margin fraction
- CSS root variables:
  - --bg, --board-frame, --board-bottom, --player, --player-dark, --computer, --computer-dark, --tie, --tie-dark, --win-ring
- DOM IDs to target:
  - #board — canvas
  - #restart — restart button
  - #message — HUD message
  - #turn — turn indicator

Appendix B — Example README snippet (copy to README.md)
- Title: Forth Connect — Connect‑4 template
- Quick start:
  - Unzip, go to folder, open index.html or run python -m http.server 8000 then open http://localhost:8000
- License: see LICENSE
- Support: support@cotopia.org