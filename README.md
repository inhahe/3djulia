# 3DJulia

The Julia set generalizes to four dimensions, but four dimensions are hard to
look at. This slices it: pick a **2D plane** or a **3D hyperplane** through the
4D set, rotate that slice on any of the 4D axes, and watch the cross-section
change as it sweeps through the fourth dimension.

Live version: <https://inhahe.com/julia4d.html>

Made with Claude Opus 4.6.

## Features

- Slice the 4D Julia set as either a 2D plane or a 3D hyperplane
- Rotate the slice on **any** 4D axis — including the rotations that move it
  through the dimension you aren't looking at
- Choose **complex** or **quaternion** arithmetic for the iteration, which give
  genuinely different sets rather than different renderings of one set
- A **"chessboard" mode** for the 2D view (my own invention — I haven't seen it
  elsewhere)

## Versions

There are three implementations of the same idea, and they are not equal:

| File | What it is |
|---|---|
| `julia4d.html` | **Browser version — the most complete one.** Self-contained, no build step; just open it. |
| `main.cpp` | C++ / ImGui / OpenGL desktop version, built with CMake. Window layout is remembered in `imgui.ini`. |
| `julia4d.py` | Python version. |
| `search.py` | Parameter search — hunts for interesting constants rather than rendering. |

If you only want to look at the thing, open `julia4d.html`.

## Building the C++ version

```
cmake -B build
cmake --build build --config Release
```

Produces `julia4d`. Requires OpenGL; CMake finds it via `find_package(OpenGL)`.

## Running the Python versions

```
pip install -r requirements.txt
python julia4d.py
python search.py
```

## License

MIT - see [LICENSE](LICENSE). Free to use, modify and redistribute; provided
as-is, with no warranty.
