# Bhai — Bengali Practice Agent: Instructions

## Purpose

Bhai is a scheduled AI agent that sends Luc two Bengali practice emails per day (8 AM and 9 PM Paris time). Each email contains 3 new beginner Bengali sentences with phonetic transcription and French translation, and keeps a flashcard website in sync so the full sentence library is always browsable.

---

## Responsibilities

1. **Send practice emails** — 3 sentences per session, twice daily, no repeats ever.
2. **Maintain the CSV** — append each sent session to `sentences.csv` as the source of truth.
3. **Sync GitHub** — push updated CSV, JSON, and flashcard site after every session.
4. **Pre-load next session** — immediately after each run, update the script with fresh sentences for the next scheduled run to prevent duplicate sends.

---

## Infrastructure

| Component | Details |
|-----------|---------|
| Email | Gmail API via OneCLI proxy (auth injected automatically) |
| Sentence store | `/workspace/agent/sentences.csv` |
| Email script | `/workspace/agent/session_email.mjs` |
| GitHub sync script | `/workspace/agent/sync_github.mjs` |
| GitHub repo | `kelu124/learn-bangla` |
| Flashcard site | https://kelu124.github.io/learn-bangla/ |
| Scheduling | NanoClaw `schedule_task` — cron `0 8 * * *` and `0 21 * * *` (Europe/Paris) |

---

## How Sessions Work

### Session numbering
Session number is computed dynamically from the CSV row count:
```js
const sessionNumber = Math.floor(rows.length / 3) + 1;
```
This means the number is always correct even after corrections or manual edits.

### Duplicate prevention — the pre-load pattern
The critical rule: **immediately after each session runs, update `session_email.mjs` with 3 new sentences for the next session.** If you don't, the script will re-send the same sentences when the next scheduled task fires.

The sentences array is hardcoded in the script. The workflow is:
1. Scheduled task fires → script runs with pre-loaded sentences
2. Email sent, CSV appended, GitHub synced
3. Agent immediately updates script with next session's sentences
4. Done — next task will fire cleanly

### The CSV format
```
id,bengali,phonetic,french,sent_at
1,"আমি ভাত খাই।","Aami bhaat khaai.","Je mange du riz.",2026-05-20T08:01:23.456Z
```
IDs are sequential. Never reuse an ID. Check the last row's ID before composing new sentences.

---

## Email Sending

The script (`session_email.mjs`):
1. Reads the CSV to compute session number and last ID
2. Sends an HTML email via Gmail API with MIME base64url encoding
3. Appends the 3 new sentences to the CSV
4. Calls `sync_github.mjs` via `execSync`

### Email structure
- **From:** kelu124@gmail.com
- **To:** kelu124+bhai@gmail.com
- **Subject:** `Bangla practice - session N` (ASCII only)
- **Body:** Bengali script (bold) → phonetic (italic) → French, plus a link to the flashcard site
- **Footer:** "Sent by Bhai • Your Bengali practice assistant"

### Gmail API call
```js
const res = await fetch('https://gmail.googleapis.com/gmail/v1/users/me/messages/send', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ raw: encoded }), // base64url-encoded MIME message
});
```
The OneCLI proxy intercepts this and injects the OAuth token automatically.

---

## GitHub Sync (`sync_github.mjs`)

After each session, three files are pushed to `kelu124/learn-bangla`:

1. **`sentences.csv`** — the full sentence history
2. **`sentences.json`** — JSON array of all sentences (used by the flashcard site)
3. **`index.html`** — the flashcard site with the sentence data inlined as `const _raw = [...]`

The sync script fetches each file's current SHA from GitHub before updating (required by the Contents API to avoid 409 conflicts):
```js
const sha = await fetch(`https://api.github.com/repos/kelu124/learn-bangla/contents/${filename}`)
  .then(r => r.json()).then(d => d.sha);
await fetch(`https://api.github.com/repos/kelu124/learn-bangla/contents/${filename}`, {
  method: 'PUT',
  body: JSON.stringify({ message: '...', content: base64content, sha }),
});
```

---

## Bengali Sentence Guidelines

### Level
Beginner — simple present tense, everyday vocabulary. Topics: greetings, food, weather, household actions, transport, nature, time of day.

### Structure per sentence
| Field | Example |
|-------|---------|
| Bengali script | `আমি ভাত খাই।` |
| Phonetic (English) | `Aami bhaat khaai.` |
| French translation | `Je mange du riz.` |

### Phonetic conventions used
- ব → B, ভ → Bh, প → P, ফ → Ph
- Long vowels doubled: আ → aa, ই → i, উ → u
- Retroflex sounds: ট → T, ড → D
- অ → o (short), আ → aa (long)
- চ → chh (aspirated), জ → j
- ড় → r (flapped r)

### Uniqueness check
Before composing new sentences, check the full CSV. Never repeat a Bengali sentence that has already been sent. With 200+ sentences in the corpus, avoid re-using sentence patterns too closely — vary subjects, verbs, and topics.

---

## Scheduling

Two tasks run permanently:
- **Morning:** `0 8 * * *` (Europe/Paris) — 8:00 AM
- **Evening:** `0 21 * * *` (Europe/Paris) — 9:00 PM

To recreate them using NanoClaw's `schedule_task`:
```
prompt: "You are Bhai, Luc's Bengali practice agent. Send a daily Bengali practice email. [full instructions...]"
cron: "0 8 * * *"   # morning
cron: "0 21 * * *"  # evening
timezone: "Europe/Paris"
```

---

## Replicating This System

To run Bhai from scratch:

1. **Set up the repo** — fork or clone `kelu124/learn-bangla`. The repo needs `sentences.csv`, `sentences.json`, and `index.html`.
2. **Connect Gmail and GitHub** — via OneCLI credential vault (or equivalent proxy that injects auth headers).
3. **Create `sentences.csv`** — with header `id,bengali,phonetic,french,sent_at`. Can start empty.
4. **Create `session_email.mjs`** — using the structure above. Hardcode 3 starter sentences.
5. **Create `sync_github.mjs`** — to push the three files to GitHub after each run.
6. **Schedule two tasks** — 8 AM and 9 PM, Paris time.
7. **Maintain the pre-load pattern** — after every run, update the sentences array in the script before the next scheduled task fires.

The agent needs: read/write access to its workspace, outbound HTTPS (proxied for auth), and the ability to run Node.js ESM scripts.

---

*Maintained by Bhai — Luc's Bengali practice agent. 70 sessions completed as of June 2026.*
