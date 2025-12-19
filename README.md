# Glass Calc (Single-File Calculator)

A fully functional, responsive **single-file** calculator web app (HTML + CSS + JavaScript in one file) with a dark glassmorphic UI, keyboard support, and a scientific mode.

## Run

Open `index.html` in any modern browser.

For the **Help** modal (which loads `/README.md`) to work reliably, run a local server in this folder and open the app via HTTP:

```bash
python -m http.server
```

## Modes

- **STD mode**: standard calculator keys + VAT tools + currency conversion.
- **SCI mode**: scientific keys (toggle using the top-right mode pill).
- **TRZ mode**: same UI/behavior as **STD** (duplicate).

The mode pill shows the **current** mode (cycle on click: **STD → TRZ → SCI → STD**).

## Display

- **Top line**: current calculation context.
- **Bottom line**: live input / result.

## Log panel

- The Log panel is **hidden by default**.
- Use the **LOG** pill (left of **DEG**) to show/hide it.
- The panel appears to the right of the calculator (stacks below on small screens).
- Use **Export CSV** to download the current log as `calc-log.csv`.
- Use **Clear** to remove all log entries (also clears persisted log storage).
- Each log row shows an **Action** (e.g. `2 + 10`, `20 BGN`) and the resulting **Value**.
- The log keeps the **last 50 values** and restores them on reload.
- **Double-click** a log item to copy its value to the calculator display.

## Help

- Click **Help** (left of **LOG**) to open a modal that renders this README (loaded from `/README.md`).
- If you opened `index.html` via `file://`, some browsers will block fetching `/README.md`; use the local server command above.

## Features

### Core

- `+`, `−`, `×`, `÷`
- `+/-` (toggle sign)
- Decimal input (`.`)
- `C` (clear all)
- `DEL` (backspace)

### Scientific (SCI mode)

- `√x` (square root)
- `x^y` (exponent)
- `%` (percent)
- `π` (pi constant)
- `EXP` (scientific notation entry, e.g. `5.6e-4`)
- Trigonometry: `sin`, `cos`, `tan`
- **INV** toggles:
  - `sin/cos/tan` → `sin⁻¹/cos⁻¹/tan⁻¹`
  - `ln` → `e^x`
  - `log` → `10^x`
- Logs:
  - `ln(x)` (natural log)
  - `log(x)` (base-10 log)
- Random tools:
  - `Coin` → random `0` or `1`
  - `Dice` → random integer `1` to `6`
  - `Rand` → random number in `[0.0, 1.0)`

### Angle mode (DEG/RAD)

The **DEG/RAD pill** changes how trig is interpreted:

- Normal trig (`sin/cos/tan`): input is treated as degrees or radians.
- Inverse trig (`sin⁻¹/cos⁻¹/tan⁻¹`): output is returned as degrees or radians.

### Currency conversion (STD mode)

Buttons appear only in STD mode:

- `EUR`: converts **BGN → EUR** using the fixed peg `1 EUR = 1.95583 BGN`
- `BGN`: converts **EUR → BGN** using the same peg

### VAT (STD mode)

Buttons appear only in STD mode:

- `+VAT`: adds **20% VAT** (net → gross): $x \to x \cdot 1.2$
- `-VAT`: removes **20% VAT** (gross → net): $x \to x / 1.2$
- `00`: rounds the current value to **2 decimals**
- `000`: rounds the current value to **3 decimals**

### Negative number entry

- You can type a leading `-` to start a negative number (e.g. `-5`, or `5 * -3`).
- When entering `EXP` notation (e.g. `5e`), `+` / `-` applies to the exponent sign.

## Persistence

The app stores your last used:

- **Mode** (`calc_mode` cookie: `STD` / `SCI` / `TRZ`)
  - Legacy fallback: `calc_sci` (`1` / `0`)
- **DEG/RAD** (`calc_angle` cookie)
- **Log panel visibility** (`calc_log` cookie)
- **Log entries** (saved in `localStorage` as `calc_log_entries`)

## Keyboard Shortcuts

### Digits & basic ops

- `0–9` digits
- `.` decimal
- `+ - * / ^` operators
- `Enter` or `=` equals
- `Backspace` delete
- `Esc` clear

### Scientific shortcuts

- `s` → `√`
- `i` → `sin`
- `o` → `cos`
- `t` → `tan`
- `p` → `π`
- `r` → toggle `RAD/DEG`
- `e` → `EXP` (enter scientific notation)
- `l` → `ln` (or `e^x` when INV is ON)
- `g` → `log` (or `10^x` when INV is ON)

### Sign toggle

- `n` / `F9` → `+/-`

## Notes

- `ln(x)` and `log(x)` require `x > 0`; otherwise the calculator shows `Error`.
- Division by zero shows `Error`.
- Very long results are automatically **compacted for display** (often into a shorter `...e...` form) so they fit the screen.

## Layout

- The `0` key is single-width.
- The `=` key spans two columns.
