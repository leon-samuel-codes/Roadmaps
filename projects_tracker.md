# Projects Tracker

One tracker for everything that isn't FRIDAY (FRIDAY has its own master
roadmap file) and isn't calendar dates (those live in `calendar_master.md`).
Freeze/UT rules: see `calendar_master.md`.

---

## 1. Mini-projects: build order and status

Six standalone mini-projects, each with its own plan file and its own
script. Build them independently — there is no shared code between
projects. The `plan_*.md` files are the authoritative specs.

> **TODO (plan links):** the links below point at the repo root
> (`plan_*.md`). It could not be verified that these files live in
> `plans/`. Confirm that `plans/` exists and holds the six files (or move
> them there), then prefix every plan link in the table and the Status list
> with `plans/`.

### Shared setup

```bash
pip install matplotlib numpy scikit-learn
```

Needed by: Function Plotter, Linear Regression, Projectile Motion, Sorting
Comparison. The Organizer and the Linked List/Stack/Queue projects are pure
standard library.

### Build order

| # | Project | Plan file | Script | Prereqs |
|---|---------|-----------|--------|---------|
| 1 | Downloads Folder Organizer | [plan_downloads_organizer.md](plan_downloads_organizer.md) | `organize_downloads.py` | dicts, `os` basics, error handling |
| 2 | Linked List / Stack / Queue | [plan_linked_list_stack_queue.md](plan_linked_list_stack_queue.md) | `linked_list_stack_queue.py` | basic classes intro (`class`, `__init__`, `self`) |
| 3 | Function Plotter | [plan_function_plotter.md](plan_function_plotter.md) | `function_plotter.py` | functions, dicts, input validation |
| 4 | Projectile Motion Simulator | [plan_projectile_motion.md](plan_projectile_motion.md) | `projectile_motion.py` | `math`, loops, input validation; matplotlib |
| 5 | Sorting Algorithms Comparison | [plan_sorting_comparison.md](plan_sorting_comparison.md) | `sorting_comparison.py` | lists, `random`, `time`; **recursion primer, done as the first step of this project, before merge sort** |
| 6 | Linear Regression From Scratch | [plan_linear_regression.md](plan_linear_regression.md) | `linear_regression.py` | do this **after** your first pandas + matplotlib portfolio project |

> The order above (1 → 2 → 3 → 4 → 5 → 6) follows each spec's own
> prerequisites. The organizer is first (familiar concepts), the
> linked-list project is the OOP warm-up before refactoring FRIDAY into
> classes, and linear regression is saved for last because it lands best
> once plotting real data is comfortable. `calendar_master.md` follows this
> order.

### Status

- [ ] 1. Downloads Folder Organizer — **not built yet**; first project after
      Phase R/S (see `calendar_master.md`) — plan: [plan_downloads_organizer.md](plan_downloads_organizer.md)
- [ ] 2. Linked List / Stack / Queue — not started — plan: [plan_linked_list_stack_queue.md](plan_linked_list_stack_queue.md)
- [ ] 3. Function Plotter — not started — plan: [plan_function_plotter.md](plan_function_plotter.md)
- [ ] 4. Projectile Motion Simulator — not started — plan: [plan_projectile_motion.md](plan_projectile_motion.md)
- [ ] 5. Sorting Algorithms Comparison — not started (recursion primer first) — plan: [plan_sorting_comparison.md](plan_sorting_comparison.md)
- [ ] 6. Linear Regression From Scratch — not started — plan: [plan_linear_regression.md](plan_linear_regression.md)

Tick each box when that project passes its plan's **Verify** section.

### Conventions (all projects)

- One standalone script per project at the repo root.
- Module docstring with a short description and usage examples.
- Phase comments in the code matching the plan's phases.
- `main()` function + `if __name__ == "__main__":` guard.
- Optional extension phases are marked in each plan — core "definition of
  done" comes first, extensions after.

---

## 2. Finished projects

**Governing sequence for both change lists:**
5 Dec UTs → FRIDAY Phase R → Phase S → `calculator_changes.md` →
`todo_changes.md`. Phase R is the only hard gate; Phase S sits between R and
the change lists.

### 2a. Scientific Calculator (CLI) — retired, on GitHub

Status: done and pushed. It's a finished first project as-is.

**Change list:** [`calculator_changes.md`](calculator_changes.md) — runs
after the 5 Dec UTs and after FRIDAY Phase R (and Phase S), about 45
minutes, **before** `todo_changes.md`. Do not use `format(value, ".12g")` for
`format_result()`: it shortens big whole numbers (e.g. factorial results),
which the change list keeps exact.

**Optional cleanup tracks (parked as light-day mini-bosses, not required;
lower priority than the change list — do not start any of them before it is
finished):**
- **Track 1 — cleanup:** rename to snake_case, extract the repeated
  zero-division check into one helper, add docstrings. (Corrupted history
  file handling: see `calculator_changes.md`.)
- **Track 2 — more math power:** log/ln, exponentials, nCr/nPr, GCD/LCM,
  prime check, basic statistics, 2x2/3x3 matrix operations (ties into the
  Math syllabus)
- **Track 3 — history & UX:** delete single history entry, search history.
  (Timestamps and re-prompt on invalid input: see `calculator_changes.md`.)
- **Track 4 — engineering habits:** split into `calculator_ops.py` /
  `history.py` / `main.py`, type hints, pytest tests
- **Track 5 — interface:** tkinter GUI or small Flask app, argparse for
  one-shot calculations

### 2b. To-Do Manager — ✅ DONE, on GitHub

3-module split (`main.py` / `tasks.py` / `storage.py`), no globals. Parked
V2 ideas: due dates (datetime), priorities (dict key).

**Change list:** [`todo_changes.md`](todo_changes.md) — runs after the 5 Dec
UTs, after FRIDAY Phase R (and Phase S), and **after** `calculator_changes.md`.
About 1.5 hours; task IDs (with a `tasks.json` file-format migration) come
before any V2 feature. Back up `tasks.json` first.

---

## 3. Portfolio pipeline (supports the AI/ML programme application — "Christ" in earlier notes; 1–2 year horizon)

Listed in the suggested build order (`calendar_master.md` follows this
order):

1. **Data analysis project** (pandas + matplotlib) — clean a public dataset,
   visualize trends, write up 3–4 concrete insights. Table stakes for any
   AI/ML role.
2. **ML classifier** (scikit-learn) — proper train/test split, at least two
   evaluation metrics, not just `.fit()` once.
3. **ML model served via an API** (FastAPI/Flask) — take a trained model
   from #2 and wrap it in one working endpoint.
4. **Small CV/NLP project** using a pretrained model — image classifier,
   sentiment analyzer, or summarizer. Talking point on deep learning without
   needing to train from scratch.
5. **FRIDAY, matured** — voice, routing design (decision pending, see
   `friday_roadmap.md`), OOP structure, modes, remote control. The flagship —
   most original, most likely to get real interview follow-up questions.
   Ongoing throughout, since FRIDAY grows continuously rather than
   finishing on a fixed date.

**Optional extras (pick 1–2 if there's room):**
6. A practical automation tool beyond the mini-projects above (scheduled
   backup script, more elaborate file organizer)
7. A second, narrower Telegram/Discord bot (study reminder or habit
   tracker) — reuses FRIDAY Phase 13 skills as a standalone resume project

**Order:** #1 → #2 → #3 → #4, with #5 ongoing throughout.

---

## 4. Long-term horizon — AI Engineer roadmap (reference only)

A saved external roadmap (@byswergiu) for the years beyond this tracker —
apply existing models to build products, in strict sequence, ~10 hrs/week.
Not a current schedule; nothing here starts before the Phase B/D windows
(calendar phases). The AIE-n labels below belong to this external roadmap
only and are unrelated to FRIDAY Phases 7–14 and to calendar Phases A–D.

- **AIE-0 — Python & software foundations** (2–4 wks): Python, APIs/HTTP,
  Git/GitHub. Build: an app that calls a public API.
- **AIE-1 — ML fundamentals** (2–3 wks): train/inference, loss, gradient
  descent intuition. Build: a small scikit-learn/PyTorch model.
- **AIE-2 — How LLMs work** (2–3 wks): tokenization, embeddings,
  attention, sampling, limitations. Build: raw API experiments.
- **AIE-3 — Reliable LLM apps** (3–4 wks): prompting, structured outputs
  (Pydantic), tool calling, retries/fallbacks.
- **AIE-4 — Embeddings & RAG** (4–6 wks): semantic search → vector DB →
  chunking → RAG → hybrid search/reranking.
- **AIE-5 — Agents, tools & MCP** (3–5 wks): the agent loop, multi-tool
  use, memory, MCP, agent reliability.
- **AIE-6 — Multimodal AI** (1–2 wks): vision, speech, documents — one
  modality at a time.
- **AIE-7 — Fine-tuning & open models** (1–2 wks, mostly conceptual):
  prompting vs RAG vs fine-tuning, LoRA/PEFT, local quantized inference.
- **AIE-8 — Evaluating AI systems** (2–4 wks): evals, RAG retrieval vs
  generation checks, model comparison.
- **AIE-9 — Production AI** (3–5 wks): deploy (FastAPI/Docker), observability,
  optimization/caching, security (prompt injection, secrets).
- **AIE-10 — Frameworks** (as needed): LangChain/LangGraph only now that
  you know what they abstract.
- **AIE-11 — Build, document, get good** (ongoing): 2–3 deep projects —
  LLM app with tools, RAG app with real retrieval, multi-tool agent — then
  evals, monitoring, deployment on one of them.

Its own three rules apply: follow the order, build every time, treat the
time ranges as guidance. Finishing the phases doesn't make you an AI
engineer — finishing them with three projects you can explain in detail
does.

---

Rule that's worked so far, keep following it:
**one new Python concept → one visible project upgrade → test → commit → push.**

*Updated 22 Nov 2026. Freeze/UT rules: see `calendar_master.md`. FRIDAY
tracked separately in `friday_roadmap.md`. Change lists:
`calculator_changes.md`, then `todo_changes.md`, after Phase R/S.*
