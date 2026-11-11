# Karten — spec

Personal German flashcard PWA. No backend, no accounts. Everything stored in localStorage.

## Stack
Plain HTML + CSS + vanilla JS. No framework, no build step. Hosted on GitHub Pages, installed via Chrome "Add to Home screen".

## Files
```
index.html      # UI, styles, all app logic
sw.js           # service worker: network-first for HTML, cache-first for assets
manifest.json   # PWA manifest
icon-192.png
icon-512.png
```

## Screens (tab bar)
1. **Study** — spaced repetition, one card at a time, flip to reveal, 4 rating buttons
2. **Decks** — folders of cards; create a deck, tap it, type a word to generate a card
3. **Cards** — searchable list of all cards across decks
4. **Settings** — API key, model, extra instructions, export/import

## Data model

### Card
```json
{
  "id": "string",
  "deckId": "string",
  "de": "springen",
  "en": "to jump",
  "example": "Er ist über den Zaun gesprungen.",
  "example_en": "He jumped over the fence.",
  "note": "",
  "word_type": "verb",
  "forms": "springt – sprang – ist gesprungen",
  "grammar": "sein-Verb",
  "governs": "",
  "alternatives": "hüpfen = to hop/skip (small repeated jumps)",
  "collocations": "ins Wasser springen; über einen Zaun springen",
  "register": "neutral",
  "level": 0,
  "due": 1731312000000,
  "lastReviewed": null,
  "added": 1731312000000
}
```

**New grammar fields (added 2026-11):**

| Field | Description | Example |
|-------|-------------|---------|
| `word_type` | verb / noun / adjective / adverb / preposition / phrase | `"verb"` |
| `forms` | Key inflections (see rules below) | `"springt – sprang – ist gesprungen"` |
| `grammar` | Short tags for notable behaviour | `"sein-Verb, trennbar (auf|stehen)"` |
| `governs` | Case or preposition + case the word requires | `"helfen + Dat"` |
| `alternatives` | Other German equivalents of the English word | `"hüpfen = hop/skip (small jumps)"` |
| `collocations` | 1–2 common combinations | `"ins Wasser springen; über den Zaun springen"` |
| `register` | casual / neutral / formal | `"neutral"` |

**Forms rules:**
- **Verbs:** 3rd-person present ONLY if stem changes, then Präteritum, then Perfekt with aux. E.g. `"springt – sprang – ist gesprungen"` or `"machte – hat gemacht"`.
- **Nouns:** article + plural. E.g. `"der Sprung, die Sprünge"`.
- **Adjectives:** comparison only if irregular. E.g. `"gut – besser – am besten"`. Otherwise `""`.
- Other word types: `""`.

**Backwards compatibility:** existing cards without the new fields display fine — missing fields are treated as `""`.

### Deck
```json
{ "id": "string", "name": "German Words", "created": 1731312000000 }
```

### Settings (localStorage key `settings`)
```json
{
  "apiKey": "sk-…",
  "model": "gpt-4.1-mini",
  "extraInstructions": "I'm around B1.",
  "direction": "de-en",
  "lastExport": 1731312000000
}
```

## Spaced repetition
Levels 0–6. Intervals (days): `[0, 1, 3, 7, 14, 30, 60]`.

| Button    | New level             | Next due                    |
|-----------|-----------------------|-----------------------------|
| Again     | 0                     | now + 1 minute              |
| Hard      | max(1, level)         | now + interval[new level]   |
| Good      | min(6, level + 1)     | now + interval[new level]   |
| Excellent | min(6, level + 2)     | now + interval[new level]   |

Due = `due <= end of today`.

## OpenAI call
`POST https://api.openai.com/v1/chat/completions` with structured outputs (`response_format: json_schema`). No `temperature`. Returns exactly 1 card per request.

**System prompt instructs:**
- Fill all grammar fields accurately and concisely (no full conjugation tables)
- Write example sentence to demonstrate the most important grammar point (Perfekt for sein-Verbs, case/preposition in use, etc.)
- When input has multiple German equivalents, pick the most common for `de`, explain others in `alternatives`
- Use `""` for any field that doesn't apply

## Card back layout (study screen)
1. Answer (large, bold)
2. Grammar line: `[word_type] · forms · grammar tags · governs` — skips empty parts
3. Example sentence (italic) + translation
4. Info block (muted): `also` alternatives · `e.g.` collocations · `register` if not neutral
5. Note (purple box) if present

## Offline
Service worker: network-first for `index.html`, cache-first for icons/manifest. OpenAI never cached. Studying/rating/export work offline; generation requires internet.

## Out of scope
Accounts, sync, backend, audio, images, stats, Play Store.
