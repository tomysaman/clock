# Flip Clock

A single-file, dark-themed flip clock inspired by [flipclock.app](https://flipclock.app/). No build step, no server, no external dependencies — just open `index.html` in a browser.

## Features

- Large HH:MM flip-card display with a once-per-minute flip animation
- Blinking `:` separator (every 2 seconds)
- Dark theme by default (no pure white), with a light mode toggle
- 6 accent color presets
- Fullscreen support (button, `F` key, or double-click)
- Preferences (color, dark/light) persisted in `localStorage`

## Usage

Open `index.html` directly in any modern browser — no server required.

## Design notes

- Updates the DOM once per minute (aligned to the minute boundary via `setTimeout`), not once per second, to minimize CPU usage.
- The colon blink is a pure CSS `animation`, so it costs no JavaScript timer.
- The flip timers pause entirely when the tab is hidden (`visibilitychange`) and resume on return.

See `docs/plans/flip-clock-plan.md` for the original design plan.
