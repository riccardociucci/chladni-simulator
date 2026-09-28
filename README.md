# Chladni Simulator

Interactive browser-based Chladni figure simulator. No installation, no dependencies — open the HTML file and it runs.

Chladni figures are the geometric patterns that appear when sand on a vibrating plate accumulates along nodal lines, the points where vibration is zero. This simulator lets you explore them in real time, in both particle and vector form, on square or rectangular plates.

---

## Files

| File | Description |
|------|-------------|
| `index.html` | Particle mode — 25,000 sand grains in real time |
| `chladnisimulator.html` | Full version — particles + vector rendering with SVG export |

The previous version, with an always-square plate, is tagged `v1-originale`.

---

## Plate format

| Format | Behaviour |
|--------|-----------|
| 1:1 · 4:5 · 9:16 | The plate keeps the chosen ratio and fits the available space |
| Custom | Width and height in pixels (100–4000): the real canvas resolution, displayed scaled to fit |

On non-square formats, **Adapt** decides how the square pattern becomes a rectangle:

| Mode | What it does |
|------|--------------|
| Stretch | Recomputes the pattern on the rectangle with the same node counts — it looks stretched |
| Density | Scales the node counts along the long axis so node spacing stays constant (default) |
| Crop | Shows a centred window of the square pattern — the 1:1 figure, cropped |

Density rounds each scaled node count to the nearest integer with the same parity as the original, because parity decides the mirror symmetries of the figure.

---

## Physics

### Mode 1 — Fourier

z(u,v) = Base · sin(π·aₓ·u)·sin(π·a_y·v) + Mirror · sin(π·bₓ·u)·sin(π·b_y·v)

At 1:1, aₓ = b_y = m and a_y = bₓ = n, the classic sin(πmx)sin(πny) + sin(πnx)sin(πmy). `m` and `n` control the number of nodes along X and Y. `Mirror = +1` produces symmetric patterns; `Mirror = −1` produces diagonal ones — shown in green in the UI. On non-square plates each term gets its own frequency per axis (see Adapt).

### Mode 2 — Sources

Four circular wave sources, draggable on the canvas:

f(x,y) = Σ cos(K · rᵢ)

Particles accumulate along interference nodes (zero-crossings of f). A Blend slider interpolates between the two modes.

---

## Vector rendering

The vector pipeline extracts nodal lines as clean, exportable SVG paths.

    gridDims()      → Nx×Ny grid, odd and coprime with the axis frequencies; Nx=Ny on square plates
    sampleField()   → field on the grid, float noise snapped to exact zero, normalised to max|z|=1
    marchSquares()  → isolines; each crossing tagged with the id of its grid edge
    chainSegs()     → stitched by edge id → closed or open polylines
    simplifyChain() → RDP with canonical start for closed chains
    chainToPath()   → SVG path (polyline or Catmull-Rom Bézier)

Fill modes: None · Band (region where |z| < threshold) · Regions (positive areas) · Cells (positive cells light, negative cells violet, each cell its own object).
Outline: optional stroke along the nodal lines, drawn as closed contours of the positive cells.
Export: SVG with the long side at 1200 px and named layers (background, fill or cells-positive / cells-negative, outline, border) — ready for Illustrator. Vectors are recomputed at about 1 export pixel per grid cell, finer than the on-screen preview.

---

## Controls

| Label          | Variable  | Range       | Default |
|----------------|-----------|-------------|---------|
| Nodes X        | m         | 1–10        | 3       |
| Nodes Y        | n         | 1–10        | 5       |
| Base           | a         | −2 / +2     | 1.00    |
| Mirror         | b         | −2 / +2     | 1.00    |
| Band thickness | BANDEPS   | 0.02–0.40   | 0.12    |
| Stroke width   | STROKEW   | 0.3–4.0     | 1.0     |
| Grains         | NP        | 500–50,000  | 25,000  |
| Vibration      | VIB       | 0.01–1.5    | 0.15    |

### Keyboard shortcuts

| Key   | Action                              |
|-------|-------------------------------------|
| V     | Toggle Particles / Vector           |
| Space | Pause / resume (particles only)     |
| R     | Reset particles + boost             |
| S     | Export SVG                          |
| F     | Fullscreen                          |
| ← →   | Navigate presets                    |
| Esc   | Exit fullscreen                     |

### Presets

43 presets across 4 groups (Base · Grid · Medium · Complex), each with a live SVG thumbnail generated via marching squares. White presets have Mirror = +1; green presets have Mirror = −1.

---

## Technical notes

Coprime resolution — the grid size is odd and coprime with the frequencies of its axis, so straight nodal lines never fall on grid lines.

Zero snapping — diagonal nodal lines (x = y, x + y = 1) do pass through grid vertices, where the field is ±1e-16 with random sign. Values below 1e-9 of the maximum are set to exactly zero and given a fixed sign, so the line comes out whole instead of in fragments.

Edge-id stitching — every crossing carries the id of the grid edge it lies on, and the two cells sharing an edge produce the same id. Contours are joined by topology, not by rounded coordinates: no merged points, no specks, and fill contours always close.

Saddle decider — ambiguous marching-squares cells are resolved with the sign of the bilinear saddle point; ties keep positive regions apart. No X junctions, so no self-touching "figure 8" paths in Illustrator.

Smooth toggle — off by default (clean polylines); when on, curves use Catmull-Rom Bézier with control vectors clamped to segLen/3 to prevent overshooting after simplification.

Deterministic simplification — closed chains are rotated to a canonical start (leftmost point, then topmost) before RDP, making the output order-independent.

Forced negative border — during fill marching squares, border samples are clamped to a small negative value so all fill contours close inside the domain — at the artboard edge in Crop mode too.

Closed outline — the outline reuses the contours of the positive cells. Every interior nodal line separates a positive cell from a negative one, so it appears exactly once, and every path is closed; where a cell touches the border the path runs along the frame.

Cells — negative cells are traced like positive ones on the inverted field. For export, closed contours are grouped by nesting depth (even = outer boundary, odd = hole of its parent), so every cell, ring-shaped ones included, is a single path with its holes. Positive and negative cells tile the plate, apart from sub-pixel gaps along the nodal lines.

Resize — changing the window or the format rescales particles and sources instead of scattering them, so a formed pattern survives.
