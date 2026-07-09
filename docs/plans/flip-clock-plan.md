# Plan: Single-file Flip Clock (flipclock.app style)

## Context

Build a standalone flip-clock web page similar to https://flipclock.app/ that can be opened directly from an HTML file in a browser — no server, no build step, no external resources. Priorities: minimal CPU usage (no animations), dark theme by default (no pure white anywhere), very large clock filling the screen, and a small settings panel (color theme, dark/light toggle, fullscreen). No timer/alarm features.

## Deliverable

A single file: `index.html` containing all HTML, CSS, and JS inline. Zero external requests (system font stack only), works from `file://`.

## Design

### Visual layout
- Full-viewport centered clock: two large "flip cards" — **HH** and **MM** — with a colon separator, mimicking flipclock.app. No seconds display (per user decision, for lowest CPU).
- Each card is a static flip-card look: rounded rectangle, subtle top/bottom half shading (CSS gradient) and a thin horizontal divider line across the middle. No animation of any kind.
- Digits sized with `min(28vw, 56vh)`-style clamping so the clock dominates any screen size/orientation.
- Default: 24-hour format.
- Dark theme default: near-black background (`#0a0a0a`), card `#181818`, digits in off-white (`#cfcfcf` — never `#fff`). Light theme inverts (light gray bg, dark digits).

### Settings (gear icon, bottom corner, fades unless hovered)
Small popover panel with exactly:
1. **Color theme** — a row of ~6 preset swatches that tint the digit color (off-white, amber, green, cyan, rose, violet).
2. **Dark / light mode** toggle.
3. **Fullscreen** button (Fullscreen API; also `F` key and double-click on the page).

### Settings persistence
- All preferences (accent color, dark/light mode) are saved to `localStorage` on every change and restored on page load, so the clock reopens exactly as last configured — works on `file://` too.
- Applied via CSS custom properties on `:root` (theme = a class + `--accent` variable; no re-rendering of the DOM).
- Fullscreen state is not persisted (browsers require a user gesture to enter fullscreen, so it can't be auto-restored).

### Low-CPU strategy
- Clock shows HH:MM only → DOM updates **once per minute**, not per second.
- Use a self-rescheduling `setTimeout` aligned to the next minute boundary (compute `ms until next minute`), so there's no polling and no drift.
- On `visibilitychange` (tab hidden), the pending timeout is cleared; on visible again, time is refreshed immediately and rescheduled — no background wake-ups.
- Update only the two text nodes that changed; everything else is static CSS. No canvas, no requestAnimationFrame, no transitions/animations.

## Verification
- Open `index.html` in a browser, or via chrome-devtools MCP: confirm correct time renders, minute rollover updates, settings persist across reload, fullscreen and dark/light toggles work, and no console errors. Take a screenshot to confirm the look.
- Sanity-check CPU: page should be fully idle between minute ticks (no timers firing more than once per minute).
