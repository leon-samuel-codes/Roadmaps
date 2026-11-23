# Scientific Calculator: Final Change List (ideas only, no code)

Merges your own plan with the code review.
**When:** after the UTs (5 Dec) and after FRIDAY Phase R (Phase S also runs
before this). About 45 minutes in total. **Do this one first, before
`todo_changes.md`.**

Sequence: 5 Dec UTs → Phase R → Phase S → this file → `todo_changes.md`.

Your verdict stands: the calculator is retired, so atomic writes, per-entry validation,
Ctrl+C handling, the data-driven menu and type hints stay **skipped** (Section D).
Learn those on FRIDAY first.

---

## Section A: Your planned changes (keep)

### A1. Timestamp on each history record
Save the date and time with every calculation when it is recorded, and show it next to
each line in the history view. Old history entries won't have a time, so the history
view must cope with a missing one instead of crashing.

### A2. A `format_result()` helper instead of rounding to 4 places
Write one small function that turns a result into clean text: no float noise like
0.30000000000000004, and no "-0" shown for zero. Whole numbers (such as factorial
results) should be kept exactly, because big integers shouldn't be shortened. Use it
everywhere you currently round, in all three calculation functions. Good moment to watch
Bro Code #15 (format specifiers) first.
**Bonus:** it also fixes the bug where "round to 6 places" gets cut back to 4.

---

## Section B: Added after the review

### B1. Stop the crashes (the README says "never crashes", but it does)
These inputs crash it today:
- a negative number to a fractional power (like -8 ** 0.5): Python makes a complex number
- zero to a negative power (your zero checks skip the `**` operator)
- a huge power (like 10 ** 400)
- typing `inf` or `nan` as a number

The ideas:
1. **Reject `inf` and `nan` where numbers are read in.** One check there stops the trig,
   ceil, floor and factorial crashes, because they all fail on those values.
2. **For the `**` operator, catch the specific errors** (divide by zero, overflow), and
   also check whether the answer came out complex. Print a friendly message and go back
   to the menu. Use specific exceptions, not a bare catch-everything, as you prefer.
3. **Set a maximum for factorial** (for example 1000), so a huge input can't freeze it.

Test: try each of the inputs above. None should crash.

### B2. Rename `.gitIgnore` to `.gitignore`
Git only reads the lowercase name. Windows hides the problem, but anyone cloning on Linux
or macOS gets `history.json` tracked. A rename that only changes the letter case needs
two steps in Git (rename to a temporary name, then to the right one). Afterwards, check
whether `history.json` is already tracked; if so, stop tracking it without deleting your
local copy, then commit.

### B3. Save the "all trig" option to history
Right now it prints the values and saves nothing. Build the values as text, print them,
and return a record the same way the other functions do.

### B4. Fix the README claim
After B1, replace the README's "never crashes" with something accurate, like "invalid
input is caught and re-prompted", unless you have tested every case.

---

## Section C: Optional, when you feel like it
- Guard the menu loop so it only runs when the file is run directly (Bro Code #41 first)
- One named constant for the tiny number used about 10 times
- One small helper for the repeated "print result, add to history, save" lines
- Corrupted history file on load: backport from FRIDAY Phase H, same pattern as
  `todo_changes.md` Section C (do the backport once, there)

## Section D: Skipped (agreed in your plan)
Atomic writes, per-entry validation on load, Ctrl+C handling, data-driven menu, type hints.

---

## Order of work
1. B2 (`.gitignore`), 2 minutes
2. B1 (crash fixes), then test each one
3. A2 (`format_result`), then A1 (timestamp)
4. B3, then B4
5. Commit after each step, so any step can be undone on its own
