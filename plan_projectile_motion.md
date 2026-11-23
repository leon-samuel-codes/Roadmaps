# Plan — Projectile Motion Simulator

**Script:** `projectile_motion.py`
**Concepts:** functions, `math` module, loops, input validation.
**New:** `matplotlib` for the trajectory plot.

**Deps:** `pip install matplotlib`

## Definition of done

Enter velocity and angle, get a correctly plotted trajectory and a printed
summary of max height/range/time. The two-angle overlay is optional polish.

## Phases

### Phase 1 — Input handling
- Ask for initial velocity, launch angle (degrees), and optional initial height
  (default `0` if the user just presses Enter).
- Convert angle to radians with `math.radians()` before using it in any trig
  function — same pattern as the calculator's trig functions.
- Write a `get_number()` loop-until-valid helper (same pattern as the calculator).

### Phase 2 — Physics calculations
Standard kinematics, no calculus. Each as its own small function (`g = 9.8`):
- Time of flight: `t = (v*sin(angle) + sqrt((v*sin(angle))**2 + 2*g*h)) / g`
  (simplifies if `h = 0`)
- Max height: `h_max = h + (v*sin(angle))**2 / (2*g)`
- Range: `R = v*cos(angle) * t`

### Phase 3 — Generate the trajectory
- Loop over time from 0 to total flight time in small steps (e.g. 0.01 s).
- At each step:
  - `x = v*cos(angle)*t_step`
  - `y = h + v*sin(angle)*t_step - 0.5*g*t_step**2`
- Store each `(x, y)` pair in two parallel lists as you go.

### Phase 4 — Plot the trajectory
- `plt.plot(x_list, y_list)`
- Axis labels: `"Distance (m)"` and `"Height (m)"`
- Title with the actual input values (velocity/angle) via an f-string
- `plt.grid(True)`, `plt.show()`

### Phase 5 — Print a summary
- After plotting, print time of flight, max height, and range, each rounded to
  2 decimal places (`round()` habit from the calculator).

### Phase 6 — Polish
- Validate that velocity is positive and angle is between 0 and 90 degrees;
  print a clear message and re-ask if not (loop-until-valid like `get_number`).
- Loop back so the user can try another launch without restarting the script.

### Phase 7 — Extension: two-angle overlay
- Ask for a second angle, compute its trajectory the same way.
- Plot both on the same axes with `label=` and `plt.legend()` to visually
  compare which angle goes farther/higher — the "wow" moment showing why 45°
  maximizes range.

## Verify

1. **45° maximizes range** for `h = 0` — compare against e.g. 30° and 60° at the same velocity.
2. **90° (straight up) gives range ≈ 0.**
3. A known case's `h_max` matches the closed-form formula.
4. Invalid inputs (velocity ≤ 0, angle 91°) re-prompt with a clear message.
5. Summary values are rounded to 2 dp and agree with the plotted curve
   (peak of the curve ≈ printed max height).