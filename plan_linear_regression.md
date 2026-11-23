# Plan — Linear Regression From Scratch

**Script:** `linear_regression.py` — plus a short write-up `linear_regression_notes.md`
**When:** do this **after** your first data-analysis portfolio project (pandas + matplotlib).
**Concepts:** functions, loops, `math`, `matplotlib` (from earlier plotting projects).
**New:** gradient descent as an algorithm — nothing new syntactically, just a new *idea*.

**Deps:** `pip install matplotlib scikit-learn` (sklearn only for the extension)

## Definition of done

Your manual gradient descent converges to roughly the same slope/intercept as
scikit-learn on the same data, you have a plot showing the fitted line against
the data points, and a short write-up explaining what happened.

## Phases

### Phase 1 — The dataset
- Hand-write a tiny believable dataset: 10–15 `(hours studied, marks scored)` pairs.
- Keep it small enough to plot and eyeball roughly where a line should go, so
  intuition exists to check the code against later.

### Phase 2 — The prediction function
- `predict(x, m, b)` returning `m*x + b`.
- This is the entire "model" — everything after this finds good `m` and `b`.

### Phase 3 — The error function
- `mean_squared_error(x_values, y_values, m, b)`: loop every point, compute
  `(actual_y - predicted_y) ** 2`, return the average.
- **Gate:** test with `m = 0, b = 0` first and print the error — it should be
  some large-ish number, confirming the function works before touching
  gradient descent.

### Phase 4 — Gradient descent, one step at a time
The core of the project — build slowly:
- Start with `m = 0`, `b = 0`, `learning_rate = 0.01`.
- For each point, compute the gradients:
  - `dm = -(2/n) * sum(x * (y - predicted_y))`
  - `db = -(2/n) * sum(y - predicted_y)`
- Update: `m = m - learning_rate * dm`, `b = b - learning_rate * db`.
- Do this update **once**, print the new `m`, `b`, and the error — confirm the
  error actually went down from Phase 3's baseline **before** looping.

### Phase 5 — Loop it
- Wrap Phase 4's single update in a loop for 1000 iterations.
- Print the current error every 100 iterations to watch it decrease.

### Phase 6 — Results
- Print the final `m`, `b`, and final error.
- `plt.scatter` the original data points; `plt.plot` the fitted line using the
  final `m`/`b`.

### Phase 7 — Extension: compare with scikit-learn
- Redo the exact same dataset with `sklearn.linear_model.LinearRegression`.
- Fit it, print `.coef_` (slope) and `.intercept_`.
- Compare against the manual `m` and `b` — they should be very close
  (within ~0.01). This is the "aha": the library isn't magical, it's exactly
  what you just built, faster.

### Phase 8 — Write it up (`linear_regression_notes.md`)
- A few sentences: what the manual version got, what sklearn got, how close
  they were, and what you'd explain to someone about what gradient descent is doing.
- Worth keeping — a good answer to "walk me through a project" in an interview
  or application.

## Verify

1. `m=0, b=0` baseline error is a sensible large number.
2. One gradient step measurably lowers the error.
3. Error printed every 100 iterations decreases over the 1000-iteration run.
4. Final `m`/`b` match sklearn's `.coef_`/`.intercept_` within ~0.01.
5. Plot renders with data points and fitted line.
6. `linear_regression_notes.md` exists and answers the write-up prompt.