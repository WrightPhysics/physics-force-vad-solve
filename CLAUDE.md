# physics-force-vad-solve

Interactive simulation for Richard Wright's Advanced Physics class (Modeling Instruction, Balanced Force Particle Model unit). Students translate between five representations of the same situation and use a Force Vector Addition Diagram (VAD) to find unknown forces.

Live at **https://wrightphysics.org/physics-force-vad-solve/**

## How the project works

- Everything is in **one file: `index.html`** (inline CSS and JS, no build step, no dependencies besides Google Fonts).
- **Deploying = pushing to `main`.** GitHub Pages serves the repo root; the site updates about a minute after a push. GitHub's CDN can serve the old version for up to ~10 minutes, so test with a cache-busting query string (`?v=2`).
- The homepage for wrightphysics.org lives in a separate repo, `WrightPhysics/wrightphysics.github.io` (one `index.html` with a card per simulation). Other sims: `physics-normal-force`, `physics-motion-diagrams`, `physics-fbd-vad` (older tool, leave it alone).
- After any JS change, syntax-check before committing: extract the `<script>` block and run `node --check` on it. Then check the page in a browser.

## Working with Richard

- He is a physics teacher and former consulting engineer. He gives feedback through screenshots; expect visual, precise requests.
- **Use his wording exactly** when he specifies text (titles, messages, labels). Don't paraphrase it.
- Make the change asked for and nothing else; mention side effects briefly.
- Commit and push only when he asks.

## Page structure

Top to bottom:

1. Title: **"Solving for Forces with your Force Vector Addition Diagram"**
2. Scenario picker: ← / dropdown / → (same as the pumpkin sim). The 4.9 ones come **first**, labeled **"BFPM 4.9 Apply The Model #N"**; the original five follow, labeled **"Scenario N: name"** (kept as before, so they're still 1–5). `SCN` and `SKETCH` are parallel arrays: reorder them together (`label` on the scenario; `name` keeps the book's title)
3. Top row, three panels: **Written Description** | **Sketch** | **Interaction Diagram (ID)**
4. Middle row, side by side: **Free Body Diagram (FBD)** (~40% width) | **Force Vector Addition Diagram (VAD)**
5. **Angle Annotations** bar at the very bottom (applies to the FBD and VAD above it)

Every representation panel except the VAD has a Hide/Show button (placeholder asks students to rebuild it). The force chips live in the VAD panel so hiding the FBD doesn't block the VAD. There are **no instruction notes** in the VAD panel (removed on purpose). The **Check my VAD** button (full width under the grid, report below it) was copied from the pumpkin sim: step 1 every force joined head to tail in one chain (`checkChain`, specific feedback for missing forces, head-to-head, tail-to-tail, forks, loose arrows, separate chains), step 2 the VAD ends where it started (all scenarios are balanced, so Δv = 0; the step 2 titles say "consistent with the motion", the messages say "scenario"). Red rings mark the problem; the report clears as soon as the VAD changes (`clearStaleCheck`, `placedSig`). Hints only, no "show me the answer".

## Physics and notation conventions

- **All scenarios are balanced (BFPM).** Students haven't studied net force yet. Current scenarios: book on table, box at rest on 30° ramp, sign on two ropes (attached to walls), sled pulled at constant velocity, lawn mower pushed at constant velocity, then the 7 from the course book's 4.9 Apply the Model (BFPM): Pulling Horizontally, Pulling Up at an Angle, Pushing Down at an Angle, Block on Incline, Pulled Up Frictionless Incline, Traffic Light, Horizontal Spring. The 4.9 scenarios use the book's problem text verbatim.
- **Type-On-By subscripts:** `F_gBE` = gravitational force on Box by Earth. Each scenario's key lists its object letters (e.g. "B = box, R = ramp, E = Earth"). Pushes by a person use `P` and "Y = you" (e.g. `F_PMY`).
- g = 10 N/kg. VAD grid: **1 square = 10 N** by default; a scenario can set `sq` for a finer grid (BFPM #7, the spring, uses 5 N) and then `mag` values are in squares of that size. **By default the VAD shows no values** on the vector labels (and none on the force chips). A **Show values** checkbox turns them on and reveals a **Scaled length (cm) / Force (N)** choice, **cm by default** (1 square = 1 cm).
- Masses are given in the written description, **not** on the sketch.

## VAD behavior (core pedagogy)

- **Vectors can be added in any order, but always head-to-tail.** Arrows snap head-to-tail when dragged near each other.
- **Direction is locked.** Arrows translate only; they can never rotate. Dragging a head moves it along a dashed line of action; pulling sideways shows "Direction is locked…".
- **Unknown forces change magnitude** by dragging the arrowhead; known forces have fixed length.
- Optional "Show lines of action" toggle.
- **Show Vector Addition Animation** button (VAD header, next to Clear VAD): replays the student's own completed VAD (every force placed and joined head to tail): each arrow grows tail to head in chain order, then the result (green ring + ΣF = 0, or the dashed gap + ΣF ≠ 0). It never shows anything the student hasn't built. If the VAD isn't complete it shows a hint instead. The real VAD is ghosted while it plays; the button becomes **Stop animation**.
- Status line at the top of the VAD panel, exact wording:
  - all placed and joined, balanced: **"The VAD ends where it started, so the forces are balanced (ΣF = 0)."**
  - all placed and joined, not balanced: **"The VAD doesn't end where it started, so the forces are unbalanced (ΣF ≠ 0)."**
  - (ΣF with italic F.) Use "ends where it started," never "closes."
- On the diagram: a gap is a dashed segment **with no arrowhead** labeled **ΣF ≠ 0** (an arrowhead would preview net-force direction, which students haven't met). A closed VAD gets a green ring and **ΣF = 0** in ink color. These appear only when every force is placed and joined.
- `CLOSE_TOL = 0.25` grid units decides "ends where it started" for both the label and the status line (matches the snap distance).

## FBD

- Dot (r = 8) at the center; **every force arrow starts at the dot's center**, dot drawn on top.
- Forces not yet in the VAD show direction only (neutral length). Once a force is in the VAD, its FBD arrow **scales with its VAD length** (common scale, updates live while dragging). When any force is placed, unplaced ones are drawn faded.
- Clicking an FBD arrow or a chip adds/removes that force in the VAD.

## Sketch (Etkina style)

- Simple line drawings; objects labeled **"Name (L)"**, inside the object where it fits.
- **System boundary:** red (#C62828) dashed, thicker than object lines (stroke-width 3.5, dash 8 5). **Where the object touches a surface, the boundary's bottom edge runs exactly along that surface line.** For the mower, the boundary hugs the top of the handle closely and closes just short of the hands, so the push crosses it.
- **Motion maps:**
  - At rest: "v = 0,  Δv⃗ = 0" above the system (v here is speed, no arrow).
  - Constant velocity: three equally spaced black dots with equal **light green** velocity arrows (`--vel`), each labeled v⃗; dots drawn **on top of** the arrows; "Δv⃗ = 0" above the row. Dark green (`--dv`) is reserved for Δv⃗ arrows.
- Vector symbols get a small arrow over the letter (`mathText` helper).
- Stick figures are tall enough that the hand is **below the shoulder** (arm reaches down to rope/handle, not up).
- Given angles are drawn with a dashed reference line and arc; annotation text has a light halo so it stays readable.

## Interaction Diagram

Richard's rules:

- Objects are ovals labeled "Name (L)".
- Interactions are **curved lines with an arrowhead at both ends**, touching both objects, arrowheads meeting the oval **perpendicular** to its edge.
- Each interaction is labeled **field, compression, extension, or shear**.
- The system has a **red dashed boundary**.
- **Negligible interactions are left out entirely** (no crossed-out lines).
- Include environment interactions too (e.g. Earth–Ground, You–Ground), not just those touching the system.
- Each interaction crossing the boundary is exactly one FBD force: field → g, compression → N (or P for a push), extension → T, shear → f. Crossing interactions are drawn in their force's color; others are gray.
- Hovering/tapping any interaction, FBD arrow, VAD arrow, or chip highlights that force everywhere (`data-f` attributes + `applyHL()`).

## Arrows: the `wright vector` style

Matches his TikZ `wright vector` style:

- Head is the arrows.meta `>` (To) tip: **two curled barbs, stroked (not filled)** with the shaft's line width.
- Proportions 7w long × 5w wide, scaled by `HEAD_SCALE = 0.6` (he chose 60% of full size).
- The head's tip lands exactly on the vector's endpoint; the round tail cap starts exactly at the tail.
- Very short vectors scale the head down so it never exceeds 60% of the arrow.

## Angle Annotations

- Multi-select: **From horizontal**, **From vertical**, **Between arrows**; separate toggles for showing them on the FBD and/or VAD.
- Right angles get a square marker, not "90°". Horizontal/vertical arrows get no horizontal/vertical angle.
- Arc radius scales with the adjacent arrows and clears the arrowhead.
- **Angle labels must sit right next to their own arc** (just outside it, or just inside for wide angles). They may sit over an arrow (the halo keeps them readable) rather than move far away.

## Labels

- All labels go through the collision-aware placer (`makePlacer`): avoid arrow shafts, arrowheads, rings, other labels, and the canvas edge. Label–label overlap is treated as unacceptable.
- Text widths are **measured** with a canvas (`tw()`), and the page re-renders after web fonts load, so measurements match what's drawn.
- Force labels are colored to match their arrows; VAD labels prefer the outside of the polygon.
- He has repeatedly flagged overlapping or far-away labels. After changes that affect layout, check several scenarios, including narrow (phone) widths.

## Visual style

- Fonts: Atkinson Hyperlegible (UI/body), STIX Two Text (math, force labels).
- Colors are CSS tokens on `:root` with light and dark versions; force colors `--f0`…`--f4`; motion maps `--vel` / `--dv`.
- Keep the page self-contained and responsive (FBD/VAD stack below ~900 px).
