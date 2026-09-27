# Glossary — build this up as you learn

## 3D Reconstruction
- **SfM (Structure from Motion)** — recovering 3D camera poses + point cloud from 2D video
- **Photogrammetry** — the broader discipline; SfM is its core algorithm
- **Feature matching** — finding correspondences across frames (SIFT → SuperPoint/LightGlue)
- **Loop closure** — recognising a revisited place so the map doesn't drift apart
- **Visual-inertial odometry (VIO)** — fusing camera + IMU; essential indoors, no GPS
- **COLMAP** — the reference SfM pipeline
- **Nerfstudio** — maintained wrapper around 3DGS training, easiest entry point
- **NeRF** — neural radiance field, (x, d) → (rgb, density). Slow, beautiful
- **3DGS (3D Gaussian Splatting)** — scene as millions of anisotropic Gaussians. Fast,
  photoreal, real-time. **The default choice in 2026.**
- **Anisotropic covariance** — per-Gaussian scale + rotation, vs. a sphere
- **Spherical harmonics** — view-dependent colour encoding
- **Densification** — splitting/cloning Gaussians where the reconstruction is poor
- **Pruning** — removing low-opacity / oversized Gaussians
- **Rasterization** — GPU-friendly splat projection, differentiable
- **.splat / .ply** — the interchange formats for splat sets

## Web Rendering
- **Three.js** — the standard browser 3D library
- **WebGL2** — the GPU API; instancing is how you draw millions of splats
- **Instancing** — one draw call, millions of primitives
- **@mkkellogg/gaussian-splats-3d** — practical browser splat renderer
- **LOD** — level of detail; 3DGS sets are multi-GB and need chunking/streaming
- **Raycasting** — mouse→3D intersection, needed for POI tagging

## Spatial / Navigation
- **Occupancy grid** — 2D costmap of free/occupied space
- **Costmap layer** — Nav2's composable obstacle layers (inflation, voxel, laser)
- **Inflation layer** — margin around obstacles so a robot doesn't scrape walls
- **A\*** — grid graph shortest path
- **D\* Lite** — replanning when the map changes, cheap incremental updates
- **Nav2** — ROS 2 navigation stack; the reference for Stage 5
- **SLAM Toolbox** — Nav2-compatible SLAM backend
- **GeoJSON** — standard spatial data encoding

## Capture
- **Loop closure trajectory** — walk a closed route so the map closes
- **Overlap ratio** — 60-70% frame overlap needed for feature matching
- **Exposure bracketing / HDR** — critical for indoor textureless walls
- **Photometric consistency** — the assumption all SfM rests on; violated by moving lights
