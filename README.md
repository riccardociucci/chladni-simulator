# Chladni Simulator

Interactive browser-based Chladni figure simulator. No installation, no dependencies — open the HTML file and it runs.

Chladni figures are the geometric patterns that appear when sand on a vibrating plate accumulates along nodal lines, the points where vibration is zero. This simulator lets you explore them in real time, in both particle and vector form.

---

## Files

| File | Description |
|------|-------------|
| `index.html` | Particle mode — 25,000 sand grains in real time |
| `chladnisimulator.html` | Full version — particles + vector rendering with SVG export |

---

## Physics

### Mode 1 — Fourier

z(x,y) = Base · sin(πmx)sin(πny) + Mirror · sin(πnx)sin(πmy)

`m` and `n` control the number of nodes along X and Y. `Mirror = +1` produces symmetric patterns; `Mirror = −1` produces diagonal ones — shown in green in the UI.

### Mode 2 — Sources

Four circular wave sources, draggable on the canvas:

f(x,y) = Σ cos(K · rᵢ)

Particles accumulate along interference nodes (zero-crossings of f). A Blend slider interpolates between the two modes.

---

## Vector rendering

The vector pipeline extracts nodal lines as clean, exportable SVG paths.

    autoRes()       → N = fixN(44·max(m,n)), odd and coprime with both m and n
    sampleField(N)  → (N+1)×(N+1) grid, normalised to max|z|=1
    marchSquares()  → isolines with bilinear saddle-point interpolation
    chainSegs()     → segment graph → continuous polylines
    simplifyChain() → RDP with canonical start for closed chains
    chainToPath()   → SVG path (polyline or Catmull-Rom Bézier)

Fill modes: None · Band (region where |z| < threshold) · Regions (positive/negative areas).
Outline: optional stroke along the nodal lines.
Export: 1200×1200 px SVG with named layers (background, fill, outline, border) — ready for Illustrator.

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
| R     | Scatter + boost (particles only)    |
| S     | Export SVG                          |
| F     | Fullscreen                          |
| ← →   | Navigate presets                    |
| Esc   | Exit fullscreen                     |

### Presets

43 presets across 4 groups (Base · Grid · Medium · Complex), each with a live SVG thumbnail generated via marching squares. White presets have Mirror = +1; green presets have Mirror = −1.

---

## Technical notes

Coprime resolution — N is always odd and coprime with both m and n, so no grid sample falls exactly on a nodal line. This is the key to perfectly symmetric patterns at any mode combination.

Bilinear saddle-point — the classic marching squares saddle ambiguity is resolved with bilinear interpolation rather than the center-sign heuristic, producing geometrically exact X crossings.

Smooth toggle — off by default (clean polylines); when on, curves use Catmull-Rom Bézier with control vectors clamped to segLen/3 to prevent overshooting after simplification.

Deterministic simplification — closed chains are rotated to a canonical start (leftmost point, then topmost) before RDP, making the output order-independent.

Forced negative border — during fill marching squares, border samples are clamped to a small negative value so all fill contours close inside the domain, producing clean closed shapes in Illustrator.
