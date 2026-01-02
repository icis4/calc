# Glass Calc - AI Instructions

## Project Overview

**Glass Calc** is a single-file calculator web app (HTML + CSS + JavaScript in one file) with glassmorphic UI design, scientific and standard modes, keyboard support, and PWA capabilities. The entire app lives in [index.html](../index.html) (~2300 lines).

## Architecture

### Core Structure (All in index.html)
- **HTML**: Calculator layout with log panel and help modal
- **CSS**: Glassmorphic design with CSS custom properties (`:root` variables at top)
- **JavaScript**: Pure vanilla JS, no frameworks—calculator state machine + UI rendering

### Key State Variables
- `current`: display string (user input or result)
- `prev`: left operand (number or null)
- `operator`: pending operation (+, -, *, /, ^, root, mod, or null)
- `overwrite`: flag to replace current when next digit is entered
- `justPressedOperator`: prevents chaining operations with just one equals press
- `calcMode`: "STD" (standard), "TRZ" (duplicate of STD), or "SCI" (scientific)
- `angleMode`: "DEG" or "RAD" for trigonometry
- `trigInverse`: boolean toggling sin⁻¹/cos⁻¹/tan⁻¹ and ln→e^x, log→10^x, √→x²
- `trigHyperbolic`: boolean for sinh/cosh/tanh when HYP is on
- `logEntries`: array of {action, value} pairs; max 50 entries, persisted to localStorage

### Data Flow
1. User input → `inputDigit()`, `setOperator()`, `equals()`, or action functions
2. State updates in handlers
3. `renderBottom()` (display line) and `renderTop()` (context line) called
4. Log updated via `appendLogValue()` or `updateTapePreviewFromState()`
5. LocalStorage persisted for mode, angle, log, and memory via `setCookie()` / `getCookie()`

## Developer Workflows

### Local Development
```bash
python -m http.server
# Opens calculator via http://localhost:8000
```
**Why HTTP?** Some browsers (Firefox, Chrome) block reading sibling files (`/README.md`, `/LICENSE`) when opening `file://index.html` directly.

### Service Worker
[sw.js](../sw.js) caches the app shell (index.html, manifest, icon) for offline access.
- **Cache name**: `glass-calc-v<VERSION>` (update in sw.js and package scripts if you version)
- **Strategy**: Network-first for navigations (app shell), cache-first for assets
- Update the `CACHE_NAME` constant when shipping changes

### Help & Documentation
- Help button opens README.md via fetch (rendered with `renderMarkdownToHtml()`)
- License button opens LICENSE as `<pre><code>` (plain text, not Markdown)
- Both use the same modal; see `openDocInModal()` and `loadHelpDoc()`

## Conventions & Patterns

### Naming
- **Functions**: camelCase; event handlers prefixed with action name (`inputDigit`, `setOperator`, `applyTrig`)
- **State**: lowercase (`current`, `prev`, `operator`, `logEntries`)
- **CSS classes**: kebab-case (`.log-item`, `.show-log`, `.show-help`)

### Computation
- Core math: `compute(a, b, op)` returns number or NaN (caller checks with `toFiniteNumberOrNull()`)
- Display formatting: `fmt(n)` returns string with smart rounding and exponential notation
- All trig/log operations use functions like `applyTrig(name)`, `applyLn()`, `applyLog10()`, etc.

### Rendering
- Called only when state changes; not continuous polling
- `renderBottom()` updates display (current or result)
- `renderTop()` updates context line (pending operation, memory status)
- Log rendered via `renderLog()` (builds DOM dynamically)

### Input Handling
- **Digits**: `inputDigit(char)` — parsed immediately, triggers `renderBottom()`
- **Operators**: `setOperator(op)` — handles chaining (e.g., "5 + 3 + 2" computes 5+3 then chains +2)
- **Equals**: `equals()` — computes pending operation, commits log step
- **Keyboard**: `keyMap` object maps key names to functions; modifiers like Ctrl+C handled explicitly
- **Paste**: `parsePastedNumber()` accepts numeric strings with spaces/commas; normalizes to decimal/exponent format

### Log Panel (Paper Tape)
- **Live preview**: While entering right operand, log shows preview row: "6 + 12 = 18"
- **Commitment**: Pressing next operator or = commits the step
- **Double-click**: Copies value to display
- **CSV export**: Converts entries to CSV (action, value pairs)
- **Persistence**: `logEntries` saved/loaded via localStorage every change

### Storage
- Cookies (custom `setCookie()` / `getCookie()`): mode, angle, memory, log visibility
- localStorage: full log entries (serialized JSON array)
- No external backend; all data is local

## Key Files & Functions

| Function | Purpose |
|----------|---------|
| `compute(a, b, op)` | Core math; returns number or NaN |
| `fmt(n)` | Formats number for display (exponential if needed) |
| `setOperator(op)` | Handles operator input; chains operations |
| `equals()` | Computes pending operation; commits log |
| `applyTrig(name)` | Applies sin/cos/tan/cot; respects inverse & hyperbolic |
| `renderBottom()` / `renderTop()` | Updates display lines |
| `renderLog()` | Rebuilds log DOM |
| `updateTapePreviewFromState()` | Live paper-tape preview during entry |

## Known Patterns & Edge Cases

1. **Negative number entry**: Unary minus (`-5`) vs. binary operator (`3 - 5`) — handled by `inputLeadingMinus()` and `maybeInputExpSign()`.
2. **Scientific notation**: Exponent marker entered with `inputExpMarker()`; then `+/-` acts as exponent sign (not operator).
3. **Factorial**: Limited to n ≤ 170 (JavaScript precision).
4. **Angle mode**: Normal trig (sin/cos/tan) respects DEG/RAD; inverse trig returns output in DEG/RAD; hyperbolic ignores angle mode.
5. **Chaining**: "5 + 3 + 2 =" computes left-to-right: (5+3) then +2.
6. **Log preview**: Only shown when actively typing right operand (checked via `prev != null && operator && !justPressedOperator`).

## Testing Checklist

- [ ] Standard operations: +, −, ×, ÷
- [ ] Decimal input and display
- [ ] Keyboard input (digits, Enter, Escape, Backspace)
- [ ] Scientific mode (sqrt, sin/cos/tan, ln, log, etc.)
- [ ] Inverse & hyperbolic toggles
- [ ] Angle mode toggle (DEG ↔ RAD)
- [ ] Log panel (show/hide, CSV export, double-click to copy)
- [ ] Help modal (F1 or Help button; fetches README.md)
- [ ] Memory buttons (M+, M−, MC, MR)
- [ ] Currency conversion (EUR, BGN)
- [ ] Paste numeric values (Ctrl+V)
- [ ] Copy current to clipboard (Ctrl+C)
- [ ] Offline (service worker caches assets)
- [ ] PWA install (manifest, icons)

## Common Tasks

### Adding a New Function
1. Create a handler: `function applyNewFeature() { /* calc logic */ }`
2. Add to button: `data-action="new-feature"` in HTML, or add key to `keyMap`
3. Add case in button click handler: `case 'new-feature': applyNewFeature(); break;`
4. Update display: call `renderBottom()` and `renderTop()` after state change

### Modifying Display Format
Edit `fmt(n)` function to change how numbers are shown (e.g., add thousands separators, change decimal places).

### Updating Help
Edit README.md; it's fetched dynamically when Help button is clicked. No rebuild needed.
