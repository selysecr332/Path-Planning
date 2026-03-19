# HW5 Path Planning — A* vs RRT/RRT*

## Overview
This project implements a full 2D path-planning pipeline on occupancy grids and compares three planners:
- `A*` on a grid graph (8-connected)
- `RRT`
- `RRT*` (with rewiring)

After planning, each trajectory is post-processed with path smoothing. The notebook also provides visual analysis, quantitative metrics, and animated search dynamics.

Main notebook:
- `HW5_Path_Planning.ipynb`

## Assignment Coverage
Implemented:
- Baseline comparison: `A*` vs `RRT`
- Improved planner: `RRT*`
- Path smoothing (gradient + collision-aware shortcut)
- Metrics comparison:
  - path length
  - runtime
  - visited nodes / sampled points
- Visualizations in Matplotlib:
  - map
  - raw and smoothed paths
  - RRT tree growth animation
  - A* explored-cell expansion animation
- Resolution study for A* (downsample factors)
- Written conclusions in notebook

## Data Sources
The notebook supports three map sources (switch with `MAP_SOURCE` in the map cell):
- `png` — custom black/white occupancy PNG maps (`hw5_occ_map_01.png`, `hw5_occ_map_02.png`, `hw5_occ_map_03.png`)
- `movingai` — MovingAI `.map` format (`hw5_movingai_sample.map`, `56x56`)
- `random_pr` — PythonRobotics-style random rectangle obstacle generator

Occupancy convention used everywhere:
- `grid[y, x] = 0` free
- `grid[y, x] = 1` obstacle

## Technical Notes
### Map loading
- PNG loading uses OpenCV first (`cv2.imread(..., IMREAD_GRAYSCALE)`), with PIL fallback if needed.
- MovingAI parser converts map symbols to binary occupancy.
- Optional obstacle inflation is implemented via `cv2.dilate`.

### A*
- 8-connected motion with diagonal cost handling.
- Returns path + metrics.
- Separate function records explored states for animation export.

### RRT
- Incremental random-tree expansion.
- Goal bias sampling.
- Collision-checked segment extension.
- Stores edge snapshots for animation.

### RRT*
- Neighbor search + best-parent selection.
- Rewiring to reduce path cost.
- Cycle-safe parent update / extraction logic.

### Smoothing
- Gradient smoothing over waypoints.
- Shortcut smoothing with collision checks.

## Produced Outputs
When running the notebook, the following artifacts are produced:
- `astar_expansion.gif` — A* explored-cell animation
- `rrt_tree.gif` — RRT tree-growth animation

The notebook also displays:
- raw vs smoothed trajectories for each planner
- a metrics table
- A* runtime vs map-resolution chart

Example A* resolution result (from current run):
- factor `1`: runtime `0.0865`, visited `9164`
- factor `2`: runtime `0.0242`, visited `3129`
- factor `4`: runtime `0.0049`, visited `695`

## How to Run
1. Install dependencies:
   - `numpy`
   - `matplotlib`
   - `opencv-python`
   - `Pillow` (for GIF export)
2. Open `HW5_Path_Planning.ipynb` in Jupyter/Colab.
3. Select map source in the map cell (`MAP_SOURCE`).
4. Run all cells from top to bottom.

## Project Files
- `HW5_Path_Planning.ipynb` — full implementation
- `hw5_occ_map_01.png` / `hw5_occ_map_02.png` / `hw5_occ_map_03.png` — custom occupancy maps
- `hw5_movingai_sample.map` — MovingAI format sample map
- `astar_expansion.gif` — exported A* animation
- `rrt_tree.gif` — exported RRT animation

## Conclusions (short)
- Lower grid resolution significantly speeds up A* due to fewer nodes, but can degrade geometric path quality and close narrow passages.
- RRT struggles in narrow corridors because random samples rarely fall into constrained channels.
- RRT* improves path quality through rewiring but still depends on successful exploration.
- Post-smoothing effectively reduces polyline zig-zags and often shortens path length.

## Reproducibility
All core logic, experiments, visualizations, and exports are inside one notebook and can be reproduced by running the cells in sequence.
