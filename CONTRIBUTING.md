# Contributing

Thanks for contributing! This repo is intentionally lightweight: the calculator is a **single-file app**.

## Project structure

- `index.html` — HTML + CSS + JavaScript (all in one file)

## How to run locally

No build step is required.

- Open `index.html` in your browser, or
- Use a simple local server (recommended for consistent behavior):

```powershell
Set-Location "c:\Projects\html\calc"
python -m http.server 5173
```

Then open `http://localhost:5173/`.

## What to change (and what not to)

- Keep it **single-file** unless there’s a strong reason to split.
- Avoid adding heavy dependencies.
- Prefer small, focused changes that don’t redesign the UI without agreement.
- Maintain the existing **dark glassmorphic** look and current layout rules.

## Code style

- Keep changes readable and minimal.
- Prefer clear function names over clever one-liners.
- When adding features, update any related keyboard shortcuts and on-screen hints.
- Comment non-obvious calculator logic (state transitions, edge cases, domain checks).

## Testing checklist

Before opening a PR, please sanity-check:

- Basic ops: `+ - * /`, decimals, `C`, `DEL`, `=`
- SCI/STD toggle behavior (label shows the *next* mode)
- DEG/RAD toggle behavior for trig and inverse trig
- INV behavior:
  - `sin/cos/tan` ↔ inverse forms
  - `ln` ↔ `e^x`, `log` ↔ `10^x`
- Cookies:
  - Sci mode (`calc_sci`) persists after refresh
  - Angle mode (`calc_angle`) persists after refresh
- Random tools: `Coin`, `Dice`, `Rand`
- Currency tools in STD mode: `EUR` (BGN→EUR), `BGN` (EUR→BGN)
- Keyboard support: digits, operators, Enter/Esc/Backspace, and any added shortcuts

## Submitting changes

1. Fork the repo and create a feature branch.
2. Make your changes.
3. Open a PR with:
   - What changed
   - How to test
   - Any edge cases handled

## Reporting bugs / requesting features

When filing an issue, include:

- Steps to reproduce
- Expected vs actual result
- Browser + OS
- Screenshot/video if relevant
