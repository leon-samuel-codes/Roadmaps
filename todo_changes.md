# To-Do Manager: Final Change List (ideas only, no code)

Merges your own plan with the code review.
**When:** after the UTs (5 Dec), after FRIDAY Phase R (Phase S also runs
before this), and after `calculator_changes.md`. About 1.5 hours in total.

**One-way change: back up tasks.json before step 1; commit after every step.**

Sequence: 5 Dec UTs → Phase R → Phase S → `calculator_changes.md` → this file.

Your verdict stands: do task IDs before any V2 features (priorities, due dates).
Type hints and the data-driven action dict stay skipped.

---

## Section A: Your planned changes (keep)

### A1. Task IDs instead of list position
- Give every task a permanent ID when it is created. Keep a counter (the next free ID)
  and save it alongside the tasks.
- Add a "find task by ID" function, and use it instead of position numbers.
- The list view shows the ID instead of the row number. IDs are never reused, so after a
  delete there will be gaps (1, 2, 4). That is correct: "task 4" always means the same task.
- **The file format changes:** `tasks.json` goes from a plain list to an object holding
  the counter and the list. Your existing `tasks.json` must be converted the first time it
  is loaded (give the old tasks IDs 1, 2, 3... and set the counter after the last one),
  or the new code will crash on it. **Back up `tasks.json` first.**

### A2. One reusable "pick a task" step
Make one function that: shows the current list, asks which ID, checks that it exists, and
returns the task (or nothing if cancelled or invalid). Complete and delete both use it.
This removes the repeated validation and means you never act on a stale number.

### A3. Confirmation before delete
Ask "Delete this task? (y/N)" and only delete on a clear yes. Anything else cancels.

### A4. "Already done" check
If the task is already completed, say so and change nothing, instead of silently
re-saving the same thing.

### A5. Ctrl+C / Ctrl+D in input
Catch those two specific exceptions in the number-reading function and treat them as
"cancelled". In the main menu, treat a cancel as Exit.

---

## Section B: Added after the review

### B1. Reject blank tasks
Trim spaces from the text and refuse an empty task, with a message.

### B2. Only save when something changed
Make each action report whether it actually changed anything (yes or no). The main loop
saves only on yes. After switching to the new format, do one real action (like adding a
task) so the new format is written to disk.

### B3. Small tidy-ups, free while you're editing
- Replace the "completed equals True" comparison with a plain check
- Rename the misleading variable in the load function (a list called "task")
- Only move print statements out of a function if you are already editing it

---

## Section C: Backport after Phase R (do these once, here)
- Handle a corrupted `tasks.json` at startup — backport from FRIDAY Phase H
- Atomic saving (write to a temporary file, then swap it in) — backport from
  FRIDAY Phase H
- `tasks.json` is found relative to the folder you run from, so running elsewhere silently
  starts with an empty list — same bug as FRIDAY's memory path; take the fix from
  FRIDAY Phase R item 4

## Section D: Skipped (agreed in your plan)
Type hints, the data-driven action dict, full separation of printing from logic.

---

## Order of work
1. Back up `tasks.json`
2. A1: conversion of the old file, then add, find and list by ID (include B1,
   rejecting blank tasks, when you touch "add")
3. A5 (cancel handling), then A2 (pick a task)
4. A4 (already done), then A3 (delete confirmation)
5. Update the main loop as in B2, apply the B3 tidy-ups only where you are
   already editing, run one action to save the new format, then commit
6. Optional: the Section C backport, once, after the steps above

Commit after every step so any step can be undone on its own.

## Test it by trying to break it
- Add a blank task: refused
- Delete task 2 of 3, then complete task 3: the ID should still be 3
- Complete a finished task: "already done", nothing changes
- Press Ctrl+C at an ID prompt: no traceback
- Answer no (or just Enter) at the delete confirmation: nothing is deleted
