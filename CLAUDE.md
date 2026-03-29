# Reading Wonderland

Interactive phonics and reading app for a young child. Single-file HTML app (`index.html`).

## Architecture

Everything lives in one self-contained `index.html` — HTML structure, CSS, and JS inline. No build step, no dependencies beyond Google Fonts (Fredoka + Quicksand).

## 6 Progressive Levels

| Level | Section | Content | Unlock Condition |
|-------|---------|---------|------------------|
| 1 | The Alphabet | 26 letters — name, phoneme sound, emoji, example word | Always open |
| 2 | Two-Letter Sounds | ~95 consonant+vowel blends (ba, be, bi…) | 13 letters visited or 5 quiz correct in L1 |
| 3 | Three-Letter Words | ~213 CVC words by short-vowel family | 20 blends visited in L2 |
| 4 | Four-Letter Words | ~1,100 words (CVCC, CCVC, common) | 30 correct in L3 |
| 5 | Five+ Letter Words | ~737 words in alphabetical groups | 50 correct in L4 |
| 6 | Sentences | 114 sentences with emoji illustrations | 50 correct in L5 |

All levels can also be unlocked via the "Unlock All" button on the home screen.

## Key Technical Details

- **Speech**: Web Speech API (`SpeechSynthesis`). Prefers Samantha voice on Mac, falls back to any `en-US` voice. Rate 0.7–0.8 for child-friendly pace.
- **Persistence**: All progress stored in `localStorage` under key `readingWonderland`. Reset button on home screen clears it.
- **Emoji mappings**: `EMOJI_MAP` object maps ~966 words to emojis. Words without a mapping show without an emoji.
- **Phonemes**: `PHONEME_MAP` maps each letter to a speakable phoneme approximation (e.g., `b` → `"buh"`, `s` → `"suh"`). TTS can't produce bare consonants, so `-uh` suffixes are used.
- **Level 2 blends**: Say the blend directly (tap "ba" → hear "ba"). Individual phoneme sounding is Level 1 only — TTS distorts isolated consonants.

## Code Structure (inside index.html)

- **CSS** (~570 lines): Variables in `:root`, component styles, animations (confetti, float, shake)
- **HTML** (~30 lines): Screens for home + 6 levels, each with a `level-container` div
- **JS** (~900 lines):
  - Data: `EMOJI_MAP`, word arrays (`CVC_WORDS`, `FOUR_LETTER_WORDS`, `FIVE_LETTER_WORDS`), `SENTENCES`, `LEVEL_CONFIG`, `PHONEME_MAP`
  - State: `defaultState()`, `loadState()`, `saveState()`, `addStars()`, `tryUnlock()`
  - Speech: `speak()`, `speakPhoneme()`, voice selection
  - Navigation: `goHome()`, `openLevel()`, `showScreen()`
  - Per-level renderers: `renderLevel1()` through `renderLevel6()`, each builds its own UI dynamically
  - Games: match (pick emoji for word), spell (tap letters to build word), quiz (letter identification), sentence scramble

## Design Principles

- **Large touch targets** (60px min) for small fingers on iPad
- **"Storybook" aesthetic** — warm earthy palette, rounded corners, dot-texture background, playful animations
- **Sound it out** is the core interaction — highlights each letter sequentially with its phoneme, then says the whole word
- **Progressive difficulty** — levels unlock as the child demonstrates mastery
- **No external APIs or servers** — works offline once loaded

## Adding Content

- **Words**: Add to `W3`, `W4`, or `W5` arrays (3-letter, 4-letter, 5+-letter). Add emoji mappings to `EMOJI_MAP`.
- **Sentences**: Add to `SENTENCES` array as `{ text, emojis }` objects.
- **Letters**: `LETTERS` array at top of data section (all 26 already present).
