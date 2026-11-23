# FRIDAY Project Roadmap (Master)

## Current Status: Phases 1–6 ✅, Phase Q ✅, Phase H ✅ Complete

| Phase | What it added                                                                                       |
| ----- | --------------------------------------------------------------------------------------------------- |
| 1     | Persistent memory — name + facts, saved to `memory.json`                                            |
| 2     | Module split — `main.py` / `memory.py` / `ai.py` / `commands.py`                                    |
| 3     | Rule-based time/date commands (`get_time()`, `get_date()`)                                          |
| 4     | Open apps & web search — `webbrowser`, `os.startfile`, `APPS` dict                                  |
| 5     | Conversation trimming — `trim()` caps message history sent to the model                             |
| 6     | Structured facts — `remember category.key = value` instead of raw sentences                         |
| Q     | Pre-freeze quick wins — `facts`, `forget`, `/help`, `<user_facts>` prompt fix, date-in-prompt       |
| H     | Hardening — atomic writes, corrupted-file recovery, per-turn `chat()` error handling                |

Repo: on GitHub (`leon-samuel-codes/friday`), module structure `main / memory / ai / commands`.
Phase Q committed as `7cb9d49`; old roadmap file removed from repo in `1794640`
(now tracked outside the repo, `.gitignore`d).

**Resolved from the original bug list** (mid-session memory not updating,
missing `continue` after remember, no chat error handling, unbounded
history, duplicate facts) — all fixed by Phases 1, 5 and 6, or made moot by
the key-value fact model. No longer tracked as open issues.

---

## 🔧 Model/runtime fix (resolved, 1–2 Oct)

Hard-freeze bug traced to two stacked causes on Ollama 0.35.0 + GTX 1660
Super: a GPU-discovery watchdog timeout that caused the model to load at
262k context instead of the correct 4096 (pagefile thrashing), plus
`qwen3:4b`'s Thinking variant generating a long reasoning trace that timed
out. Fixed by downgrading Ollama to **0.34.4** and switching the model to
**`qwen3:4b-instruct`** (confirmed: 100% GPU offload, 4096 context, fast
responses, no freeze). Old `qwen3:4b` (Thinking) still installed but unused.

---

## 🧊 Gate: no new FRIDAY feature work until after the 5 Dec UTs

Exams and the October assignments are done. As of 22 Nov 2026 this is the
pre-UT reduction runway to the 5 Dec UTs (confirmed). See
`calendar_master.md` for the current calendar — that file is the source of
truth for dates, this one just tracks project phases.

**Governing sequence:**
5 Dec UTs → Phase R → Phase S → `calculator_changes.md` → `todo_changes.md`.

Phase R below is logged (from an external code review) but not started; it
begins after the 5 Dec UTs, same as everything else past Phase H.

---

## Phase R — Review fixes (logged, not started)

**Unlocks:** `calculator_changes.md`, then `todo_changes.md`. Phase R is the
only hard gate for the two change lists. Sequence: 5 Dec UTs → Phase R →
Phase S → calculator → todo.

Found via an external AI code review of the current repo state (post Phase
Q/H). All are real, non-urgent — none break current usage, all are latent
risks. Priority order:

1. **Stale system prompt after `remember`/`forget`** — `remember`/`forget`
   save to disk but the running session's `messages[0]` (system prompt)
   isn't rebuilt, so the model doesn't see a new/removed fact until restart.
   Fix: after saving, `messages[0]["content"] = build_system_prompt(memory)`.
2. **Uncaught `FileNotFoundError` in `open_app`** — a bad/missing app path
   (e.g. Spotify) crashes the whole chat loop instead of printing an error
   and continuing.
3. **`search_web` missing URL-encoding** — a query with spaces/`?`/`&`
   produces a broken URL. Fix with `urllib.parse.quote(query)`.
4. **Memory path relative to CWD** *(shared with `todo_changes.md` Section C —
   fix it once here)* — running `main.py` from a different working directory
   silently starts with empty memory. Fix: anchor the path to
   `Path(__file__).parent`.
5. **Windows-only assumptions** (`os.environ["LOCALAPPDATA"]`, `os.startfile`)
   — not urgent since the dev machine is Windows-only, but a cheap
   `sys.platform == "win32"` guard if this ever needs to run elsewhere.
6. **Think-tag stripping edge case** — `content.split(THINK_TAG, 1)[1]`
   can return an empty string if the closing tag is the very end of the
   response. Needs a proper conditional check (confirm an opening tag
   exists and the remainder is non-empty), not just swapping to `[-1]`.
7. **Confident denial of things it doesn't know** — found 3 Oct: asked about
   the RX 9060 XT (a real 2025 card), FRIDAY said it doesn't exist. Cause is
   the small model's old training cutoff, not a code bug. Cheap fix: add a
   line to `build_system_prompt()` saying its knowledge may be outdated, to
   say "I'm not sure" about anything unrecognized, and never claim something
   doesn't exist. Reduces the problem but can't give it the answer — the
   real fix is the search-to-context item in Phase 7.

---

## Timing: hardening batches (history)

### Pre-freeze (✅ done — see Phase Q above)

### Hardening (✅ done — see Phase H above)
Logged by 2 Oct, i.e. before exams ended on 8 Oct, so it counts as a
freeze exception, like the 1–2 Oct model fix.

### Phase S — Quick wins from an AI suggestion review (logged, not started)

Sequenced **after Phase R and before** `calculator_changes.md` and
`todo_changes.md`: 5 Dec UTs → Phase R → Phase S → calculator → todo.

Reviewed an external AI suggestion list for FRIDAY improvements. Two items
agreed as small, high-value, no-new-syntax additions:

- **Streaming responses** — `ollama.chat(..., stream=True)` yields tokens as
  they generate instead of a silent wait + wall-of-text dump. ~15 lines,
  easiest big win available.
- **Chat history persistence** — currently only `memory.json` (facts) is
  saved; the conversation itself isn't. Save `messages` alongside memory and
  add a `/new` command to reset the conversation on demand.

**Tool-calling** (native Ollama `tools=[...]`, letting the model call
`remember_fact()`/`open_app()`/`search_web()` directly instead of string-
prefix commands) was also suggested and is a good direction — but it's a
full architectural redesign, not a small addition. Needs its own spec later,
not bundled into Phase S.

**⚠️ Unresolved decision — must be made before Phase 9:** three competing
designs for command routing now exist: Phase 9's planned match-case refactor,
a suggested plugin-style command-registry (class-based, `name`/`help`/`run`),
and tool-calling (model-driven function calling). Only one should be picked —
tool-calling, if chosen, makes the other two moot. Decide this once, don't
build more than one. Phase 9 and Phase 11 are blocked until it is made.

**Other suggestions from the same review, mapped onto the existing plan
rather than added as new phases:**

- Voice (Phase 10) — now has concrete library picks: `faster-whisper` (STT),
  `pyttsx3`/`edge-tts` (TTS), `pynput` (push-to-talk, no wake word needed yet)
- Vision ("what's on my screen" via `mss`/`PIL` + a vision model like
  `qwen2.5vl`) — could **be** the portfolio CV/NLP project rather than a
  separate phase
- Reminders (background thread + timestamped queue) — overlaps the existing
  Pomodoro/study-timer backlog item
- Low-priority, none urgent: a `config.json` for `MODEL`/paths/trim-length
  instead of scattered constants; a `/model` command to list and switch
  pulled models at runtime; real web-search results fetched into context
  instead of just opening a browser tab; semantic long-term memory
  (embeddings + `chromadb`) for when the `facts` dict scales to thousands
  of entries

### Soft Mode — DROPPED

Leon decided he doesn't want a personality-swap mode (`PROMPTS` dict +
`mode <name>` command). Not planned. Phase 12's `ModeManager` (action modes
that close/open apps) is separate and still on the roadmap.

---

## Phase 7 — External data (requests + APIs)

Weather dropped (not useful to Leon) — Phase 7 exists to learn `requests`
and JSON, so use lookups FRIDAY will actually use. All free, no API key.

- Watch Bro Code #65 first
- **GitHub lookup** (`github friday`) — repo info via the GitHub REST API;
  good practice digging through nested JSON
- **Dictionary lookup** (`define recursion`) — small, simple JSON, useful
  while learning programming terms
- **Web search into context** (`ask <question>` or automatic) — fetch live
  search results (e.g. the `duckduckgo_search` library) and put them in the
  prompt so FRIDAY answers from real, current data instead of the small
  model's outdated memory. This is the real fix for wrong answers about
  new products/news (see Phase R item 7). A 4B model can still ignore the
  results sometimes, so expect improvement, not perfection.
- **Wikipedia lookup** — dropped as the third option: slow to cover brand-new
  topics and overlaps the dictionary lesson
- Pick 2–3 total, don't turn FRIDAY into a command dump
- Each lookup = a standalone function that returns a string (like
  `get_time()`), with the `requests` call and error handling inside it, so
  it survives whichever routing design is chosen later (if tool-calling is
  picked, these become the tools with almost no rewriting)
- Handle failures cleanly by returning an error string, never crashing the
  loop: no internet, timeout, 404 (repo/word not found), missing JSON field
- System stats (`psutil`) is NOT an API, so it lives in Phase 8/12, not here

## Phase 8 — System tools

- `subprocess` to start/stop your own hosted server by saved PID
- Find and kill a process by port using `psutil` if you don't control the
  startup script
- `system` command: CPU, RAM, and disk usage via `psutil` (moved here from
  the Phase 7 idea list — it's a system tool, not an API)

## Phase 9 — Match-case command router

**⚠️ Blocked until the command-routing decision (match-case vs registry vs
tool-calling) is made — see the unresolved decision under Phase S. If
tool-calling or a registry is chosen, this phase changes or is dropped.**

- Watch Bro Code #38 first
- Replace the growing if/elif chain in `main.py` with `match-case`

## Phase 10 — Voice

- Watch Bro Code #64 (threading) first
- Speech-to-text: `speech_recognition` or `Vosk` (offline)
- Text-to-speech: `pyttsx3` (offline) or a cloud TTS API
- Handle turn-taking: don't listen while it's speaking

## Phase 11 — OOP refactor

**⚠️ Blocked until the command-routing decision is made (see Phase S) — the
`CommandRouter` class below assumes the registry/match-case design.**

- Watch Bro Code #46–57 (OOP block) first
- Refactor into classes: `Memory` class, `CommandRouter` class
- Mini-boss first: build a small Contact Book (`Contact` class, add/search/
  delete) to get comfortable with `self` before refactoring FRIDAY itself

## Phase 12 — Modes & PC control (action modes)

- `ModeManager` class: config-driven `close` list + `open` list per mode
  (e.g. "gaming mode" closes work apps, launches Steam)
- `psutil` to terminate processes by name
- Media-key song control via the `keyboard` module
- Stop/start your own hosted website by saved PID or by port
- Add a confirmation step before closing multiple apps — killing a process
  with unsaved work can lose data
- Hardcode your own app paths/process names rather than letting the LLM
  guess them — exact names matter for `psutil` matching

## Phase 13 — Remote control (Telegram)

- Extract all command handling into one `process_command(text) -> str`
  function shared between the terminal loop and Telegram
- Create a bot via @BotFather, install `python-telegram-bot`
- **Restrict the bot to your own Telegram user ID** — non-negotiable before
  running this outside your own laptop
- Wire incoming messages into `process_command()`

## Phase 14 — PyQt5 GUI/HUD (final)

- Watch Bro Code #66–77 first
- Visual layer showing active mode / current action — the Iron Man HUD effect

---

## Feature Backlog (not yet phased in — pull into a phase when relevant)

### Study companion features _(highest-value — recommended focus once phased in)_

- Pomodoro/study timer: "start a 45 minute study session" — mutes
  notifications, starts a timer, alerts at the end
- Voice-triggered flashcard quiz from a Q&A text file you maintain
- Auto "study mode": close distracting apps, open notes/PDFs, dim
  entertainment app notifications
- Exam countdown: "how many days to my Chemistry unit test?" from a dates file

### System awareness

- Battery %, RAM/CPU usage, disk space on request
- Volume and brightness control by voice
- Clipboard read-back
- Screenshot on command, saved with a timestamp

### Everyday utility commands

- Quick timers/alarms not tied to study
- Simple note-taking: "note this" appends to a timestamped notes file
- Web search shortcut by voice
- Weather check via a free API (overlaps Phase 7)

### Polish & trust

- Confirmation step before destructive actions (overlaps Phase 12)
- Simple tkinter/PyQt5 HUD overlay showing active mode / current action
  (overlaps Phase 14)
- Log file of commands/actions taken
- "Quiet hours" setting so it doesn't interrupt real study blocks

---

## Bro Code Viewing Order (remaining, tied to phases above)

- NOW / whenever convenient: #41 `if __name__ == '__main__'` (not yet
  watched); #13 string methods, #14 slicing (already used in practice — just
  confirm)
- Before Phase 9 (command routing): #38 match-case
- Before Phase 11 (OOP): #46–57 OOP block
- Before Phase 7: #65 requests/APIs
- Before Phase 10: #64 multithreading
- Before Phase 14: #66–77 PyQt5
- Small remaining fundamentals gaps, whenever: #12 conditional expressions,
  #15 format specifiers, #20 nested loops, #32–34 args/kwargs, #35 iterables,
  #36 membership operators, #37 list comprehensions
- Skip: game exercises (5, 17, 19, 24, 28–30, 42–45), #9/#10 conversions
  (your calculator already covers this ground)

---

_Master roadmap, updated 22 Nov 2026. Progress: Phases 1–6, Q, H complete.
Model/runtime bug resolved (Ollama 0.34.4, qwen3:4b-instruct). Gate: no new
FRIDAY feature work until after the 5 Dec UTs; sequence: 5 Dec UTs → Phase R
→ Phase S → calculator_changes.md → todo_changes.md. Phase R logged, not
started. Phase S (streaming + history persistence) logged, not started;
tool-calling flagged for its own future spec; command-routing decision
(match-case vs. registry vs. tool-calling) must be made before Phase 9 and
blocks Phases 9 and 11. See `calendar_master.md` for dates._
