# Plan — Sorting Algorithms Comparison

**Script:** `sorting_comparison.py`
**Concepts:** functions, lists, loops, `random`, `time` basics.
**New/possibly new:** **recursion** — needed for merge sort. If you haven't
covered it, watch a short 10–15 minute recursion primer first.

**Deps:** `pip install matplotlib` (extension only)

## Definition of done

All three sorts produce correct results on the same test data, and you have
printed timing numbers showing bubble/selection slowing down much faster than
merge sort as list size grows. The plot is optional polish.

## Phases

### Phase 1 — Bubble sort
- `bubble_sort(numbers)`: repeatedly step through the list, swap adjacent
  elements if out of order, repeat until a full pass makes no swaps
  (early-exit flag).
- Test on a small hardcoded list first (e.g. `[5, 2, 8, 1, 9]`) — confirm it
  actually sorts before moving on.

### Phase 2 — Selection sort
- `selection_sort(numbers)`: for each position, find the minimum of the
  remaining unsorted elements and swap it into place.
- Test the same way.

### Phase 3 — Merge sort
- `merge_sort(numbers)`: split in half, recursively sort each half, merge the
  two sorted halves back together.
- The genuinely new concept: the function calls itself on smaller and smaller
  pieces until it hits lists of size 0 or 1, which are already "sorted".
- Separate `merge(left, right)` helper that combines two sorted lists into one
  sorted list — uses only loops you already know.

### Phase 4 — Correctness check before timing anything
- Run all three on the same small list and confirm each produces an identical,
  correctly sorted result.
- Also check edge cases: empty list, single element, already sorted, reversed.
- **Don't move to timing until you trust the implementations are right.**

### Phase 5 — Generate test data
- `random.sample()` (or a loop of `random.randint()`) to build lists of
  100, 1000, 5000 elements.
- **Fair comparison:** use the *same* random list (or a copy of it) for each
  algorithm at a given size — don't regenerate per algorithm.

### Phase 6 — Time each algorithm
- Wrap each sort call with `time.time()` before and after; subtract for elapsed time.
- Print a small table: algorithm name, list size, time taken.
- Note: bubble sort at 5000 elements may take seconds — print progressively so
  there's feedback while waiting.

### Phase 7 — Extension: plot the results
- Collect timing results into per-algorithm lists.
- Plot time vs list size for each algorithm on the same graph with matplotlib,
  one `plt.plot()` per algorithm with `label=` and a `plt.legend()`.
- This is where O(n²) vs O(n log n) becomes visually obvious as the lines bend
  apart at larger sizes.

## Verify

1. **First:** correctness assertions pass — all three produce identical sorted
   output on the same list plus every edge case.
2. Timing table printed for all three sizes × three algorithms.
3. Bubble/selection times grow roughly ~10× per 10× size; merge sort grows far more slowly.
4. (Extension) The plot renders with three distinguishable curves and a legend.