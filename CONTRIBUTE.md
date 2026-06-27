# Contributing to learn-bangla

Thank you for your interest in contributing! This project is a growing Bengali sentence corpus used for daily language practice. Every sentence added makes the learning experience richer.

There are two ways to get involved:

---

## 1. Add sentences to the corpus

### Fork and submit a pull request

1. **Fork** this repository
2. **Open `sentences.csv`** — it's a plain CSV with this header:
   ```
   id,bengali,phonetic,french,sent_at
   ```
3. **Add your sentences** at the end of the file, following the format:
   ```
   212,"আমি বাজারে যাই।","Aami baajare jaai.","Je vais au marché.",2026-06-27T00:00:00.000Z
   ```
   - `id` — next integer after the last row
   - `bengali` — the sentence in Bengali script, enclosed in double quotes
   - `phonetic` — romanised transcription (see conventions below)
   - `french` — French translation (or use your own target language if you adapt this project)
   - `sent_at` — ISO 8601 timestamp; use today's date if contributing manually

4. **Update `sentences.json`** to match — it's the same data as an array of objects:
   ```json
   {"id": 212, "bengali": "আমি বাজারে যাই।", "phonetic": "Aami baajare jaai.", "french": "Je vais au marché.", "sent_at": "2026-06-27T00:00:00.000Z"}
   ```
5. **Open a pull request** with a short description of what you added and why

### What makes a good sentence

- **Beginner level** — simple present tense, everyday vocabulary
- **Short** — one clause, one idea
- **Topics** — greetings, food, weather, household actions, transport, nature, time of day, family
- **Not already in the corpus** — check `sentences.csv` before adding
- **Accurate** — if you're not a native speaker, double-check with a reference or native speaker

### Phonetic conventions

| Sound | Romanisation | Example |
|-------|-------------|---------|
| আ (long a) | aa | আমি → Aami |
| ব | B | বাড়ি → Baari |
| ভ | Bh | ভাত → Bhaat |
| প | P | পানি → Paani |
| ফ | Ph | ফল → Phol |
| চ | chh | চা → chaa |
| জ | j | জল → jol |
| ড় | r (flapped) | বাড়ি → Baari |
| Long vowels | doubled | ঈ → ii, ঊ → uu |

See [`instructions.md`](instructions.md) for the full phonetic guide.

---

## 2. Spin up your own learning agent

Want daily practice emails in Bengali (or any language) for yourself? The full setup is documented in [`instructions.md`](instructions.md). It covers:

- How to structure the sentence script and CSV
- How to send emails via the Gmail API
- How to sync to GitHub automatically
- How to schedule a recurring AI agent
- The pre-load pattern that prevents duplicate sends

You can adapt this for any language pair — just change the target language and adjust the sentence guidelines.

If you build your own version, we'd love to hear about it — open an issue or leave a note in a PR.

---

## Questions or suggestions?

Open an issue in this repository. All feedback is welcome.

*Happy learning — শুভ শিক্ষা!*
