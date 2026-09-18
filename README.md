# 4D Julia Set Explorer

**[Live Demo](http://inhahe.com/julia4d.html)** — try it in your browser (WebGL2)

Interactive real-time explorer for 4-dimensional Julia sets, with GPU ray-marched 3D volume rendering and full 4D rotation controls.

Supports two fractal types:
- **Complex** z -> z^2 + c, where (x,y) = z0 and (r,i) = c form a 4D space
- **Quaternion** q -> q^2 + c, where q is a 4D quaternion and c is a separate constant

View arbitrary 2D cross-sections or 3D hyperplane slices through the 4D fractal, rotated along any of the six 4D rotation planes. Also includes an interactive 2D Julia explorer (mouse controls z₀, with Mandelbrot outline overlay and escape orbit visualization) and a 2D Chess mode that renders the fractal as a warped checkerboard pattern.

![screenshot](https://raw.githubusercontent.com/inhahe/3djulia/main/screenshot.png)

## Building

Requires CMake 3.20+, a C++17 compiler, and Python 3 (for GLAD OpenGL loader generation at build time).

```
mkdir build && cd build
cmake .. -G "Visual Studio 17 2022"
cmake --build . --config Release
```

Or with Ninja:
```
cmake .. -G Ninja -DCMAKE_BUILD_TYPE=Release
cmake --build .
```

Dependencies (GLFW, GLAD, Dear ImGui) are fetched automatically by CMake.

## Running

```
julia4d.exe              # interactive mode
julia4d.exe --help       # show CLI options
```

## Interactive Controls

### Keyboard

| Key | Action |
|-----|--------|
| Arrow keys | Pan (translate along screen axes) |
| W / S | Move in / out (3rd basis axis) |
| Q / E | Move along 4th dimension |
| + / - | Zoom in / out |
| , / . | Rotate in active 4D plane |
| 1-6 | Select rotation plane |
| Tab | Cycle view modes (2D Slice / 2D Julia / 2D Chess / 3D Volume) |
| C | Cycle color palette |
| I / K | Increase / decrease iterations |
| [ / ] | Chess mode: decrease / increase square size |
| \ | Chess mode: toggle style (Escape / Position) |
| O | Export current 3D set as a quad mesh (`julia_mesh.obj`, Surface Nets) |
| R | Reset view |
| Escape | Quit |

### Mouse

**2D mode:**
- Drag: pan
- Scroll: zoom at cursor

**3D mode:**
- Drag: orbit camera
- Scroll: dolly camera in/out

### GUI Panel

The right-side panel provides controls for:
- **View Mode** -- 2D Slice, 2D Julia (interactive), 2D Chess (checkerboard), or 3D Volume
- **Fractal Type** -- Complex z^2+c or Quaternion q^2+c
- **4D Rotation** -- select among 6 rotation planes, rotate with buttons, current basis vectors shown below
- **Movement** -- screen pan, depth, and 4th dimension buttons (hold to repeat)
- **View Controls** -- zoom, iterations, ray-march steps, surface threshold, render scale, palette
- **Presets** -- Dendrite, Rabbit, Spiral, Siegel, Lightning, Galaxy, Mandelbrot, Sculpture

## View Modes

### 2D Slice
Standard fractal cross-section with smooth coloring and cosine palettes.

### 2D Julia (Interactive)
Pixels represent the c-plane; mouse position controls z₀ (initial z value). Mouse at center → z₀ = 0 → Mandelbrot set. Features a green Mandelbrot set outline overlay (cached to texture, recomputed only on pan/zoom) and blue escape orbit line segments emanating from the cursor.

### 2D Chess
Renders the fractal as a warped checkerboard pattern. Two styles available (toggle with `\` key or panel button):
- **Escape** — smooth iteration count + escape angle determine the checkerboard coordinates, creating bands and wedges that trace the fractal boundary
- **Position** — the last bounded z value (just before escape) displaces the original pixel position on the checkerboard, warping a regular grid according to the fractal dynamics

Starts in the Mandelbrot (c-plane) view. Square size adjustable with `[`/`]`.

### 3D Volume
GPU ray-marched volume rendering with distance-field normals and two-light Blinn-Phong shading. Defaults to Ultra quality.

## 4D Rotation Planes

The four axes are X (Re z), Y (Im z), R (Re c), I (Im c). There are six rotation planes:

| # | Plane | Description |
|---|-------|-------------|
| 1 | X-Y | Screen rotation (rotates the 2D image in-plane) |
| 2 | X-R | Mixes z_real with c_real |
| 3 | X-I | Mixes z_real with c_imag |
| 4 | Y-R | Mixes z_imag with c_real |
| 5 | Y-I | Mixes z_imag with c_imag |
| 6 | R-I | Rotates the c-plane |

Rotating in planes 2-5 blends between Julia set views and Mandelbrot-like views, creating dramatic 3D shapes.

## Quaternion Mode

Switch to "Quat q^2+c" in the GUI. The 3D position maps directly to a quaternion q = (x, y, z, 0), and c is a separate 4-component constant adjustable via drag sliders. Classic quaternion c values to try:

| c value | Description |
|---------|-------------|
| (-0.2, 0.8, 0, 0) | Bulbous lobes |
| (-0.45, 0.65, 0, 0) | Intricate filigree |
| (-0.1, 0.75, 0, 0) | Rabbit-like |
| (-0.8, 0, 0, 0) | Dendrite |
| (-0.2, 0.6, 0.2, 0.2) | Spiral arms |

## CLI Options (Headless Rendering)

Render a single frame to a BMP file without opening a window:

```
julia4d.exe --save output.bmp [options]
```

### General

| Flag | Description | Default |
|------|-------------|---------|
| `--save <file.bmp>` | Render to file and exit | (interactive) |
| `--mesh <file.obj>` | Export a Surface Nets quad mesh and exit | (interactive) |
| `--mesh-res <n>` | Mesh grid resolution (cells per axis) | 128 |
| `--iso <f>` | Mesh iso-level (escape fraction, 0–1) | 0.5 |
| `--width <n>` | Image width | 800 |
| `--height <n>` | Image height | 800 |
| `--help` | Show usage | |

### Fractal

| Flag | Description | Default |
|------|-------------|---------|
| `--c <r>,<i>` | Complex Julia constant c = r + i*i | -0.7269,0.1889 |
| `--origin <x>,<y>,<r>,<i>` | Full 4D origin | 0,0,-0.7269,0.1889 |
| `--quat` | Use quaternion iteration | (complex) |
| `--qc <a>,<b>,<c>,<d>` | Quaternion c constant | -0.2,0.8,0,0 |
| `--palette <n>` | Color palette 0-4 (Ocean, Ember, Ice, Rainbow, Mono) | 0 |
| `--iter <n>` | Max iterations | 64 |
| `--steps <n>` | Ray-march steps | 128 |

### 4D Rotation (degrees)

| Flag | Plane |
|------|-------|
| `--rot-xy <deg>` | X-Y |
| `--rot-xr <deg>` | X-R |
| `--rot-xi <deg>` | X-I |
| `--rot-yr <deg>` | Y-R |
| `--rot-yi <deg>` | Y-I |
| `--rot-ri <deg>` | R-I |

Rotations are applied in order: XY, XR, XI, YR, YI, RI.

### Camera (3D mode)

| Flag | Description | Default |
|------|-------------|---------|
| `--cam-theta <rad>` | Camera azimuth | 0.4 |
| `--cam-phi <rad>` | Camera elevation | 0.3 |
| `--cam-dist <f>` | Camera distance | 6.0 |

### Examples

```bash
# Classic Dendrite Julia set, rotated 45 degrees in X-R plane
julia4d.exe --save dendrite.bmp --c -0.7269,0.1889 --rot-xr 45

# Quaternion Julia, rose gold palette, high quality
julia4d.exe --quat --qc -0.2,0.8,0,0 --palette 1 --iter 80 --steps 200 --save quat.bmp --width 1920 --height 1080

# Mandelbrot set (c-plane slice at z0 = 0)
julia4d.exe --save mandelbrot.bmp --origin 0,0,-0.5,0 --rot-xr 90 --rot-yi 90
```

## Mesh Export (Surface Nets)

Export the current 3D Julia set as a **quad mesh** (Wavefront `.obj`) using the
Naive Surface Nets algorithm — a dual-contouring method that places one smoothed
vertex inside each surface-crossing voxel and connects the four cells sharing a
sign-changing grid edge with a quad. (This is distinct from Marching Cubes, which
produces triangles with vertices on the grid edges.)

The scalar field is the Julia escape fraction (`iterations / max_iterations`),
sampled on a `(res+1)³` grid over the same `[-2,2]³` box the ray-marcher uses.
`--iso` is the escape-fraction threshold that separates "inside" from "outside":
~1 hugs the true set tightly, lower values give a fatter surface. Works for both
complex and quaternion sets, and respects the current 4D rotation/origin.

```bash
# Quaternion set at res 200, tighter iso
julia4d.exe --quat --qc -0.2,0.8,0,0 --iter 80 --mesh quat.obj --mesh-res 200 --iso 0.6

# Complex Dendrite set
julia4d.exe --c -0.7269,0.1889 --mesh dendrite.obj --mesh-res 128
```

Interactively, press **`O`** to export the current 3D view to `julia_mesh.obj`
(uses `--mesh-res 128 --iso 0.5`).

## Automated Search

`search.py` searches for Julia c values and camera angles that best match a reference image, using silhouette (IoU) comparison:

```
py -3.14 search.py <reference.jpg> julia4d.exe [--mode complex|quat]
```

`--mode` selects the calculation type (default `quat`):
- **`quat`** — quaternion Julia `q² + c`, searching the full 4-component constant `c`.
- **`complex`** — complex Julia `z² + c`, searching the 2-component constant `c = (cr, ci)`.

Requires `Pillow` and `numpy` (`pip install Pillow numpy`).

## Rendering Architecture

Everything runs on the GPU. No pre-computed voxel grids.

**2D Slice mode:** A fragment shader computes the Julia escape time per pixel with smooth coloring and cosine palettes.

**2D Julia mode:** Same iteration shader but with z₀ controlled by mouse position. The Mandelbrot set membership is rendered to a cached texture (recomputed only on pan/zoom/resize) and edge-detected via neighbor sampling for the green outline overlay. Escape orbit lines are drawn on a Canvas 2D overlay.

**2D Chess mode:** A fragment shader iterates the fractal and maps the result to checkerboard coordinates. In Escape style, uses smooth iteration count and escape angle. In Position style, uses the last bounded z value as displacement.

**3D mode:** A fragment shader ray-marches through a [-2,2]^3 bounding box. At each fixed step along the ray:
1. The 3D position is mapped to 4D via the current basis vectors
2. The Julia/quaternion escape time is computed (for opacity)
3. At the first opaque sample, a distance-estimator gradient gives a smooth surface normal
4. Two-light Blinn-Phong shading (warm key + cool fill), dual-lobe specular, Fresnel rim, and 5-sample distance-field ambient occlusion are computed once
5. Volume compositing (front-to-back) provides natural anti-aliasing

The distance estimator (|z| * ln|z| / |dz'|, tracking the orbit derivative) is used only for normals and AO, not for ray marching. This avoids issues with the distance estimate being inaccurate in c-varying directions while still giving smooth, noise-free surface shading.

## Files

The C++/OpenGL version (`main.cpp`) is the primary, most up-to-date implementation. The HTML and Python versions are earlier prototypes kept for reference.

| File | Description |
|------|-------------|
| `main.cpp` | **Main application** -- C++/OpenGL with GPU ray-marching, distance-field normals, full lighting |
| `CMakeLists.txt` | Build system (fetches GLFW, GLAD, ImGui via FetchContent) |
| `julia4d.html` | WebGL2 version — 2D Slice, 2D Julia (interactive), 2D Chess, 3D Volume with full lighting |
| `julia4d.py` | Python/PyVista prototype (CPU voxel grid, slow) |
| `search.py` | Automated parameter search script |
| `requirements.txt` | Python dependencies for julia4d.py |

## Color Palettes

| # | Name | Description |
|---|------|-------------|
| 0 | Ocean | Teal jade |
| 1 | Ember | Rose gold / copper |
| 2 | Ice | Steel blue |
| 3 | Rainbow | Warm ivory |
| 4 | Mono | Silver / grayscale |

In 3D mode, the palette sets a single material base color. All visual variation comes from the two-light shading, specular highlights, Fresnel rim, and ambient occlusion.


## License

MIT - see [LICENSE](LICENSE). Free to use, modify and redistribute; provided
as-is, with no warranty.
