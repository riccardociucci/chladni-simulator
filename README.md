# Chladni Plate Simulator

An interactive Chladni figure simulator that runs entirely in the browser — no installation required. Chladni figures are the geometric patterns formed when sand on a vibrating plate accumulates along nodal lines, the points where vibration is zero.

### How it works

The simulation uses two blendable physics modes:

- **Fourier Mode** — computes the vibration field using the formula `Base·sin(πmx)sin(πny) + Mirror·sin(πnx)sin(πmy)`, controllable via the Nodes X, Nodes Y, Base and Mirror parameters.
- **Sources Mode** — four freely positionable circular wave sources that generate interference patterns.

The two modes blend together via the **Fusion** slider.

### Files

| File | Description |
|------|-------------|
| `index.html` | Particle simulation — 25,000 sand particles in real time |
| `chladni-vector.html` | Vector mode — clean SVG figures with fill and outline, exportable to Illustrator |

---

## index.html — Particle Simulator

### Features

- 25,000 particles simulated in real time
- Sidebar with preset groups, each with a dynamically generated SVG thumbnail
- Wave sources with lockable symmetry via the lock button
- SVG export of the current particle frame
- Fullscreen mode
- Keyboard shortcuts: `Space` pause, `R` scatter, `←/→` navigate presets, `S` export SVG, `F` fullscreen

### How to use

Open `index.html` in your browser. No external dependencies, no installation needed.

---

## chladni-vector.html — Vector Mode

Extends the particle simulator with a **Vector** rendering mode that extracts clean geometric figures directly from the vibration field using the Marching Squares algorithm. The output is a structured SVG with separate layers for background, fill, outline, and border — ready to edit in Adobe Illustrator or any vector tool.

### View modes

Switch between modes with the **Particles / Vector** buttons in the sidebar, or press `V`.

- **Particles** — identical to the original particle simulator
- **Vector** — renders the nodal figure as filled and/or outlined vector shapes

### Vector controls

| Control | Description |
|---------|-------------|
| **Fill** | `None` — no fill · `Band` — fills the nodal band (region where |z| < threshold) · `Regions` — fills all areas where z > 0 |
| **Band thickness** | Width of the nodal band used by the Band fill mode (0.02 – 0.40) |
| **Outline** | Toggles the stroke drawn along the nodal contour lines |
| **Stroke width** | Thickness of the outline stroke (0.3 – 4.0 px) |
| **Detail** | Grid resolution for contour extraction (100 – 500 cells). Higher values capture finer features |
| **Smooth curves** | Replaces straight segments with clamped Catmull-Rom Bézier curves |

### SVG export

Click **Export SVG (vector)** or press `S` while in Vector mode. The exported file contains four named groups that map to separate layers in Illustrator:

| Layer / group | Content |
|---------------|---------|
| `background` | Solid white rectangle |
| `fill` | Filled closed shapes (Band or Regions, with `fill-rule="evenodd"` for correct hole handling) |
| `outline` | Nodal contour paths |
| `border` | Closed rectangular frame matching the figure boundary |

### Keyboard shortcuts

| Key | Action |
|-----|--------|
| `V` | Toggle Particles / Vector mode |
| `Space` | Pause / resume particles |
| `R` | Scatter particles |
| `S` | Export SVG |
| `F` | Fullscreen |
| `Esc` | Exit fullscreen |
| `←` / `→` | Navigate presets |

### How to use

Open `chladni-vector.html` in your browser. No external dependencies, no installation needed.
