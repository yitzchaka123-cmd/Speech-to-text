# Voice → Text 🎙️

A tiny **speech-to-text Progressive Web App** that feels like a floating tool widget in the corner of your screen. Tap the mic, talk, and get transcribed text back — powered by OpenAI's `gpt-4o-transcribe` model with automatic language detection.

Built for **Samsung Galaxy / Android Chrome**, but works in any modern browser. Vanilla HTML + CSS + JS — no frameworks, no build step, no npm.

---

## Features

- 🎤 **Tap-to-record** — tap the mic to start, tap again to stop and transcribe (`MediaRecorder`, `audio/webm;codecs=opus`).
- 🌍 **Auto language detection** — no language is forced; the detected language is shown as a badge (e.g. 🇮🇱 עברית).
- ↔️ **RTL-aware** — Hebrew / Arabic / Persian results render right-to-left automatically.
- 🪟 **Floating-widget UI** — dark frosted-glass card pinned bottom-right on a near-transparent background, so it feels like a small overlay tool.
- 〰️ **Animated waveform** while recording + pulsing glow on the mic.
- 📋 **One-tap copy** with a ✓ confirmation flash.
- 🕘 **History** — last 10 transcriptions with timestamps and per-item copy.
- 📲 **Installable PWA** — standalone display, portrait, offline app shell, and a **"Record" home-screen shortcut** that deep-links to `/?record=true` and auto-starts recording.

---

## Quick start

### 1. Deploy
Drag this folder to [vercel.com/new](https://vercel.com/new) (or any static host with **HTTPS** — HTTPS is required for the microphone and for PWA install).

> **Deploying from GitHub?** Vercel serves your **production branch** (usually `main`) at the main URL. Other branches get a separate *preview* URL. Make sure the branch you install from is the one that has the latest code.

### 2. Add your API key
On first launch the settings panel opens. Paste your OpenAI API key (`sk-...`). It's stored **only** in your device's `localStorage` and sent **only** to `api.openai.com` — never anywhere else.

### 3. Install on your phone (Android / Chrome)
1. Open the deployed URL in **Chrome** on your Galaxy.
2. Tap the **⋮ menu → "Install app"** (some Chrome versions label it **"Add to Home screen"**, then show an **Install** dialog).
   - ✅ A correct install shows an icon with **no Chrome badge** in the corner — that's a real WebAPK.
   - ❌ A Chrome-badged icon means it was only bookmarked (no app shortcuts). This happens if the manifest lacks valid **PNG** icons.
3. **Long-press the installed icon → "Record"** → drag it onto your home screen for a one-tap "open and start recording" launcher.

---

## How it works

Audio is recorded in the browser, packaged as `multipart/form-data`, and POSTed straight to:

```
POST https://api.openai.com/v1/audio/transcriptions
  model=gpt-4o-transcribe
  response_format=json
  file=<recorded audio>
```

No `language` parameter is sent (the model auto-detects), and the `language` field in the JSON response drives the badge and RTL handling.

Everything works **offline after first load except the transcription call**, which obviously needs the network.

---

## A note on "real Android widgets"

A PWA **cannot** become a true live home-screen widget (the embedded tile kind) — that requires a native Android app. The closest equivalent here is the **"Record" shortcut** dragged onto your home screen, which gives you one-tap access straight into recording.

---

## Files

| File | Purpose |
|------|---------|
| `index.html` | The entire app — markup, styles, and logic inline. |
| `manifest.json` | PWA metadata: standalone display, icons, and the Record shortcut. |
| `sw.js` | Service worker — caches the app shell for offline use, bypasses the OpenAI API. |
| `icon-192.png`, `icon-512.png`, `icon-512-maskable.png` | App icons (PNG is required for a proper WebAPK install). |

---

## Privacy

- Your API key lives only in `localStorage` on your device.
- Audio is sent only to OpenAI for transcription.
- Transcription history is stored only in `localStorage` (last 10 entries).
- There is no backend and no analytics.
