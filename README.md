# Karten

Personal German flashcard app with spaced repetition. Runs entirely in the browser — no backend, no accounts. Cards and your API key stay on your device.

## Deploy to GitHub Pages

1. Create a new **public** repository on GitHub (e.g. `queCards`).
2. Upload all files: `index.html`, `sw.js`, `manifest.json`, `icon-192.png`, `icon-512.png`.
3. Go to **Settings → Pages**, set Source to **Deploy from a branch**, branch `main`, folder `/ (root)`. Save.
4. After a minute, your app is live at `https://<your-username>.github.io/<repo-name>/`.

## Install on Android (Chrome)

Open the URL in Chrome → tap the **⋮** menu → **Add to Home screen**. The app opens full-screen with no browser chrome.

## First run

Go to **Settings**, paste your OpenAI API key (stored only in your browser's localStorage), and optionally add extra instructions (e.g. "I'm around B1. Always include article and plural for nouns.").

Then go to **New**, type a topic (or tap a quick-pick chip), and hit **Generate**.

## Files

| File | Purpose |
|------|---------|
| `index.html` | Entire app — UI, styles, logic |
| `sw.js` | Service worker for offline use |
| `manifest.json` | PWA metadata |
| `icon-192.png` | App icon (home screen) |
| `icon-512.png` | App icon (splash screen) |

## Privacy

Your API key and all card data are stored in `localStorage` on your device only. Nothing is sent anywhere except your API key to `api.openai.com` when generating cards.
