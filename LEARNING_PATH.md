# NammaSpace — Concept Map & Learning Path

**What it is:** build an end-to-end intelligent spatial engine that turns standard
smartphone captures of an indoor space into an interactive 3D digital twin — walkable in
a browser, searchable by Point of Interest, navigable with collision-free paths.

- Competition: Techfest 2026-27, IIT Bombay. Prize ₹1,50,000. Team up to 4.
- Round 1 due **31 Oct** (online). Round 2 due **30 Nov** (online). Round 3 **16–17 Dec**,
  on-site at IIT Bombay campus.
- All deliverables: technical abstract · **live web viewer link** · public code repo · video.

Verified from two independent third-party listings of the official page; the official
`techfest.org/competitions/nammaspace` returned 403/timeout to every automated fetch, so
**confirm the SOP and abstract format on the official page before building.**

---

## The pipeline, end to end

```
phone video  →  pose / camera tracking  →  3D reconstruction  →  browser viewer
                                                                    ↓
                              POI tagging + search  ←──────────────┤
                                                                    ↓
                                        collision-free pathfinding
```

Five stages. Most people will get stuck at stage 3 and try to skip it. Don't.

---

## Stage 1 — Capture & pose estimation (weeks 1–2)

**Concepts**
- **Structure from Motion (SfM) / photogrammetry**: reconstructing 3D geometry from many
  2D views. This is how the pipeline gets camera poses.
- **Feature matching**: SIFT (classical), then learned descriptors — **SuperPoint**,
  **LightGlue** (the modern default; fast, robust, CPU-capable).
- **Incremental vs batch SfM**: COLMAP does both; you will end up tuning which.
- **Loop closure & place recognition**: why revisiting a corridor doesn't corrupt the map.
  Visual vocabulary / bag-of-words.
- **IMU + visual-inertial fusion**: phones have gyroscopes. **OpenVINS**, **VINS-Fusion**
  are the practical references for VIO. This matters enormously indoors where there's no GPS.

**Tools:** COLMAP, OpenVINS, Nerfstudio
**Gotcha:** indoor lighting is the single biggest reconstruction killer. You will need
diffuse light, no direct sun on shiny surfaces, and to move slowly. Plan your scan route
like a SLAM problem: overlap ~60-70 %, loop back to the start.

---

## Stage 2 — 3D reconstruction: NeRF vs Gaussian Splatting (weeks 2–3)

This is the core decision of the whole project. **Learn 3DGS first.**

**3D Gaussian Splatting (3DGS)** — Kerbl et al. 2023. Represents a scene as millions of
anisotropic 3D Gaussians, each with position, covariance, opacity, and spherical-harmonic
colour. Trained per-scene, renders photoreal in real time. This is what almost every
"scan a room with your phone" product now uses.

**NeRF** — Mildenhall et al. 2020. A neural radiance field: position + view direction →
colour + density. Beautiful, slow to train, and slower to render than 3DGS.

**Why 3DGS wins here:** faster training (minutes to an hour, not hours to days), real-time
rendering, and it handles the sparse-ish captures a phone walk produces far more
gracefully. The instant-NGP and Mip-NeRF lines matter less to you.

**Concepts**
- Differentiable rasterization — why you can't just use a normal z-buffer rasteriser
- Anisotropic covariance: **scale + rotation** per Gaussian, not a sphere
- Spherical harmonics for view-dependent colour (shadows, reflections)
- Adaptive density control: split/clone/prune Gaussians during training
- Densification + pruning schedules
- **COLMAP → 3DGS** is the standard path; Nerfstudio wraps this neatly
- Memory: a full room can be 2–5 GB of Gaussians. Plan for level-of-detail / streaming.

**Papers, in the order worth reading**
1. Kerbl et al., *3D Gaussian Splatting for Real-Time Radiance Field Rendering* (SIGGRAPH 2023) — read this first
2. A Gaussian Splatting Primer (2024) — best single overview, has code
3. Zwicker et al., *3D Gaussian Splatting for Real-Time Radiance Field Rendering* (survey)
4. Barron et al., *3D Gaussian Splatting as Markov Chain Monte Carlo* — why it converges
5. Szymanowicz et al., *3D Gaussian Splatting as Markov Chain Monte Carlo* / **Deblur-GS**
   — if you want a genuine contribution, indoor blurry captures are an open problem

**Tools:** Nerfstudio, gsplat, Nerfstudio Viewer, Postshot / Luma / Polycam for comparison
**Gotcha:** you can defer rendering to the browser, but training needs a GPU. Check
whether you have one available and how long a scene takes to train before promising a date.

---

## Stage 3 — Browser-based 3D viewer (weeks 3–4)

**This is the deliverable that will disqualify you if it's a local build.** It has to be a
URL an evaluator clicks.

**Concepts**
- **Three.js**: scene graph, cameras, render loop, the modern default
- **WebGL2** and what it means for splat rendering (no compute shaders; instancing is how
  you get millions of primitives)
- **@mkkellogg/gaussian-splats-3d** — the practical library. Loads `.splat`/`.ply`
  splat files, renders in browser, supports camera control
- Alternatives: **antimatter15/splat**, PlayCanvas
- Camera controls: orbit vs first-person walk. You'll want both — orbit for inspection,
  first-person for the "roaming" the video must show
- Progressive loading: a 3 GB splat set won't load instantly. LOD / chunking / streaming
- **Three.js + ROS 2 or an Isaac Sim nav layer**, so the navigation in Round 2 is config
  rather than a rewrite. This is the architectural decision that pays off in Round 2.

**Tools:** Three.js, @mkkellogg/gaussian-splats-3d, Vite
**Gotcha:** deployment. GitHub Pages won't take a 3 GB asset. You need a host that
allows large static files — check before you build, not after.

---

## Stage 4 — POI tagging & search (Round 2, weeks 5–7)

**Concepts**
- POI data model: id, name, category, 3D position, optional 2D projection, metadata
- Tagging UX: click-to-place in 3D, with raycasting from camera through mouse
- Text search: string normalisation, fuzzy matching (Levenshtein / trigram), ranking
- **Vector search** if you go semantic: sentence-transformers + a vector DB, with spatial
  pre-filtering
- GeoJSON / coordinate conventions
- Persistence: SQLite or a JSON store server-side, so tags survive reloads

---

## Stage 5 — Obstacle-aware pathfinding (Round 2, weeks 6–8)

**This is where your robotics background is an unfair advantage.** You already know most of this.

**Concepts**
- **Occupancy grid / point-cloud costmap** — standard ROS 2 nav stack vocabulary
- **A\***, then **D\* / D\* Lite** if the map changes
- Nav2 is the reference implementation: costmap layers, planners, controllers
  (`nav2_bringup`, `nav2_slam_toolbox`)
- Inflation layer: keep the robot off walls by a margin
- Collision checking: swept-volume vs just radius
- Multi-floor: elevators, stairs, connectivity between levels — indoor-specific
- Desktop pathfinding: it's 2D once you've projected the floorplan, but the height
  threshold and floor graph are where it gets interesting

**Tools:** Nav2, `nav2_slam_toolbox`, OMPL, MoveIt 2 (if the "robot" is a manipulator)
**Gotcha:** you need a navigable representation. Deriving floor plans from splats is
non-trivial — you may need depth estimates from the SfM point cloud, not the splats.

---

## Stage 6 — On-site capture & live pitch (16–17 Dec)

- Capture a venue you've never seen, on the day, to a working standard
- 10-minute pitch + 5-minute technical Q&A
- This is an **operations** problem as much as a technical one: pre-build everything, make
  capture a scripted sequence, keep a fallback demo video ready

---

## What you already have, and don't relearn

| You know | Where it shows up here |
|---|---|
| ROS 2, Nav2, MoveIt 2, TF | Stage 5, directly |
| Isaac Sim, USD pipelines | Stage 3, if you render via a sim |
| OpenCV, camera work | Stage 1, feature extraction |
| Gazebo | Stage 5, if you simulate a rover rather than a cursor |
| Python, git, ROS packaging | Everything |

**The genuinely new ground for you is Stage 2** (NeRF/3DGS math) and **Stage 3** (web
rendering). Everything else is a re-application of what you do daily.

---

## Suggested order of attack

1. **Week 1** — Get 3DGS running on a public test scene. Nerfstudio's `ns-train` on the
   garden dataset. Do this before anything else: if it doesn't work on a known scene, it
   won't work on yours, and you want that failure now.
2. **Week 2** — Load it in a browser with @mkkellogg/gaussian-splats-3d. This is the
   highest-risk unknown in the project.
3. **Week 2** — Record a scan of one room in your apartment following the SOP you'll
   publish.
4. **Week 3** — Reconstruct your own scan, not a public one. This is where the real
   problems appear.
5. **Week 3** — Ship the simplest possible web viewer with free-roaming + the video.
6. **Week 4** — Abstract, README, polish, early submission. Do not leave it to week 5.

**Submit early.** Round 1 is the one that matters; Round 2 is a filter. A mediocre Round 1
that lands keeps you in the competition.

---

## Open questions to resolve early

- [ ] **Eligibility** — a listing said "open to all students with a valid institutional ID."
      You graduated May 2026. Confirm this doesn't disqualify you before building.
- [ ] The official **capture SOP** — what exactly must the scan follow? Overlap? Speed?
      Room count? This is the one document you must get.
- [ ] Abstract **format and page limit** — nobody's stated it.
- [ ] Is the **viewer link** allowed to be localhost/ngrok for Round 1, or must it be
      publicly hosted?
- [ ] **Asset hosting limits** — 3+ GB splat sets don't fit on GitHub Pages.
- [ ] GPU availability for training, and a realistic per-scene training time.
- [ ] Do they want a **mesh** as well as a splat set, or is splat-only acceptable?
