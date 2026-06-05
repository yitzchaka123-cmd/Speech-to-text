# CLAUDE.md

Guidance for Claude Code (and other AI assistants) working in this repository.

## What this is

A single-page **speech-to-text PWA**. Vanilla HTML/CSS/JS, **no frameworks, no build step, no npm, no backend**. Three source files plus icons. The entire app is meant to stay self-contained and statically hostable (Vercel).

## Files

- `index.html` — the whole app. HTML, CSS (in a `<style>` block), and JS (in one IIFE `<script>`) all inline. **There is no bundler** — edit this file directly.
- `manifest.json` — PWA manifest. `display: standalone`, portrait, theme `#0a0a0a`. Contains the `shortcuts` array with the **Record** shortcut → `/?record=true`.
- `sw.js` — service worker. Cache-first for the app shell; **must never cache or intercept `api.openai.com`** (it early-returns on that origin and on non-GET requests). Bump `CACHE` (e.g. `stt-shell-vN`) whenever shell assets change, and keep the `SHELL` list in sync with the real files.
- `icon-192.png`, `icon-512.png`, `icon-512-maskable.png` — generated PNG icons.

## Hard requirements (don't regress these)

- **OpenAI transcription**: `POST https://api.openai.com/v1/audio/transcriptions`, model **`gpt-4o-transcribe`**, sent as `multipart/form-data`.
- **Do NOT send a `language` parameter** — the model must auto-detect. The `language` field from the JSON response drives the badge and RTL.
- **Never hardcode the API key.** It's entered on first run and stored in `localStorage` (key: `stt_openai_key`). History lives under `stt_history` (max 10 entries).
- **RTL**: Hebrew/Arabic/Persian/Urdu/Yiddish results must render with `dir="rtl"`. See `RTL_LANGS` in `index.html`.
- **PNG icons are mandatory for installability.** Chrome on Android will NOT create a WebAPK with SVG-only icons — it falls back to a plain bookmark (Chrome-badged icon, no app shortcuts). If you touch icons, keep real 192px + 512px PNGs referenced by path in `manifest.json`.

## UI intent

A small **floating frosted-glass widget**, ~260px, pinned bottom-right, on a near-transparent body (`background: rgba(0,0,0,0.05)`) so it reads as an overlay tool, not a full-screen app. Keep animations smooth/snappy. Mic has a pulsing glow while recording; a subtle animated waveform shows during capture.

## Regenerating icons

Icons are generated with Python + Pillow (`pip install Pillow`). The generation script is not committed; it draws a rounded-rect dark background (`#0a0a0a`) with a purple (`#7c5cff`) microphone glyph. The maskable variant keeps the glyph within ~80% safe zone. If you regenerate, also bump the SW `CACHE` version.

## Testing / verifying

There is no test suite. To sanity-check before committing:

- `node --check sw.js`
- `node -e "JSON.parse(require('fs').readFileSync('manifest.json'))"`
- For installability issues, the real signals are: HTTPS, a valid manifest (name, start_url, `display: standalone`, **PNG** 192+512 icons), and a registered service worker with a fetch handler. A Chrome-badged home-screen icon == not a real WebAPK install.

## Git

- Development branch: `claude/speech-to-text-pwa-yUAzR`.
- Push with `git push -u origin <branch>`. Don't open PRs unless asked.
