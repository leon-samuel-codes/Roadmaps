# Plan — Function Plotter

**Script:** `function_plotter.py`
**Concepts:** functions, dicts, loops, f-strings, input validation.
**New:** `matplotlib` (plotting), `numpy` (`linspace` instead of a manual x-value loop).

**Deps:** `pip install matplotlib numpy`

## Definition of done

Pick a function type from a menu, enter its parameters, and see a correctly
labeled graph. The extension is optional — the core project is done without it.

## Phases

### Phase 1 — Input handling
- Menu of function types: linear, quadratic, sin/cos, exponential.
- Ask which type, then ask for that type's parameters:
  - linear → `m`, `c`
  - quadratic → `a`, `b`, `c`
  - sin/cos → amplitude/frequency (or plain `sin(x)`/`cos(x)`)
  - exponential → parameters for `a * exp(b*x)`
- Dispatch dict mapping type name → builder function, same pattern as FRIDAY's
  `APPS` dict:
  ```python
  FUNCTION_TYPES = {
      "linear": build_linear,
      "quadratic": build_quadratic,
      ...
  }
  ```
- Each builder returns `(y_values, equation_title)` so the title string is built
  from the actual parameters via an f-string.

### Phase 2 — Generate x-values
- Range: `-10` to `10`, step equivalent to 200 points (range constants at top of script).
- Prefer `numpy.linspace(-10, 10, 200)` — easier and more standard than a manual loop.

### Phase 3 — Compute y-values
- Small per-type functions using only arithmetic:
  - linear: `m*x + c`
  - quadratic: `a*x**2 + b*x + c`
  - sin/cos: `amp * sin(freq * x)` (numpy ufuncs work element-wise on the linspace)
  - exponential: `a * exp(b*x)`
- A list comprehension or direct vectorized expression both work.

### Phase 4 — Plot it
- `import matplotlib.pyplot as plt`
- `plt.plot(x_values, y_values)`
- `plt.xlabel(...)`, `plt.ylabel(...)`
- `plt.title(equation_title)` — the f-string from Phase 1 showing the real equation
- `plt.grid(True)` for readability
- `plt.show()`

### Phase 5 — Polish
- Validate parameters where it matters (e.g. amplitude ≠ 0 for a sin wave —
  a zero amplitude makes the graph meaningless). Not required, just nicer.
- Loop back to the menu after each plot instead of exiting, so the user can
  plot multiple functions in one run. Include a "quit" option.

### Phase 6 — Extension
- **Two functions on one axes:** plot a second function before `plt.show()`,
  with `label=` on each plot call and `plt.legend()` to distinguish them.
- **Mark roots/intercepts:** for linear/quadratic, solve `y = 0` algebraically
  (quadratic: `np.roots([a, b, c])`; linear: `-c/m`) and mark each root with
  `plt.scatter([root_x], [0])`.

## Verify

1. Plot one function of each type.
2. For a quadratic with known vertex (e.g. `a=1, b=0, c=-4`), confirm the vertex
   sits at `x = 0` and roots at `±2`.
3. Confirm the title shows the exact parameters entered.
4. Confirm the menu returns after each plot and "quit" exits cleanly.