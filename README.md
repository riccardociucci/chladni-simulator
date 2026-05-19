# Chladni Plate Simulator

An interactive Chladni figure simulator that runs entirely in the browser — no installation required. Chladni figures are the geometric patterns formed when sand on a vibrating plate accumulates along nodal lines, the points where vibration is zero.

### How it works

The simulation uses two blendable physics modes:

- **Fourier Mode** — computes the vibration field using the formula `Base·sin(πmx)sin(πny) + Mirror·sin(πnx)sin(πmy)`, controllable via the Nodes X, Nodes Y, Base and Mirror parameters.
- **Sources Mode** — four freely positionable circular wave sources that generate interference patterns.

The two modes blend together via the **Fusion** slider.

### Features

- 25,000 particles simulated in real time
- Sidebar with preset groups, each with a dynamically generated SVG thumbnail
- Wave sources with lockable symmetry via the lock button
- Vector SVG export
- Fullscreen mode
- Keyboard shortcuts: `Space` pause, `R` scatter, `←/→` navigate presets, `S` export SVG, `F` fullscreen

### How to use

Open `index.html` in your browser. No external dependencies, no installation needed.
