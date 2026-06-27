# learn-bangla

A Bengali language learning corpus and flashcard site, built and maintained by a daily practice agent.

🃏 **[Practice with flashcards →](https://kelu124.github.io/learn-bangla/)**

---

## What this repo contains

| File | Description |
|------|-------------|
| `sentences.csv` | The full sentence corpus — 210+ sentences, 70+ sessions |
| `sentences.json` | Same data in JSON, used by the flashcard site |
| `index.html` | The flashcard web app (Bengali → phonetic → French reveal) |
| `session_email.mjs` | Node.js script that sends the daily practice email |
| `sync_github.mjs` | Node.js script that syncs the corpus to this repo |
| `instructions.md` | Full documentation for replicating the agent setup |
| `CONTRIBUTE.md` | Guide for contributing new sentences |

---

## The sentence corpus

Each sentence has four fields:

| Field | Example |
|-------|---------|
| Bengali script | `আমি ভাত খাই।` |
| Phonetic transcription | `Aami bhaat khaai.` |
| French translation | `Je mange du riz.` |
| Timestamp | `2026-05-20T08:01:23.456Z` |

Sentences cover everyday beginner topics: greetings, food, weather, household actions, transport, nature, time of day. No sentence is ever sent twice.

---

## How it works

An AI agent called **Bhai** sends two practice emails per day (8 AM and 9 PM Paris time) to a single learner. Each email contains 3 new Bengali sentences. After every session:

1. The 3 sentences are appended to `sentences.csv`
2. `sentences.json` and `index.html` are regenerated
3. Everything is pushed to this repo automatically

The flashcard site at [kelu124.github.io/learn-bangla](https://kelu124.github.io/learn-bangla/) always reflects the latest corpus.

---

## Methodology

- **Spaced repetition by volume** — new sentences every session, no repeats, building vocabulary steadily
- **Three-layer format** — script → phonetic → translation, so the learner can engage at whichever level they're comfortable
- **Beginner focus** — simple present tense, common vocabulary, short sentences
- **Consistency over intensity** — two short sessions a day rather than one long one

---

## Run your own agent

See [`instructions.md`](instructions.md) for a complete guide to replicating this setup with your own AI agent, email address, and target language.

---

## Contribute

See [`CONTRIBUTE.md`](CONTRIBUTE.md) to add sentences to the corpus or adapt this project for your own learning.

---

*210 sentences · 70 sessions · started May 2026*
