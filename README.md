# nammaspace-learning

Concept map and working notes for the **NammaSpace** competition (Techfest 2026-27,
IIT Bombay) — building an end-to-end intelligent spatial engine that turns smartphone
captures of an indoor space into an interactive 3D digital twin.

**Read [`LEARNING_PATH.md`](LEARNING_PATH.md) first.** It has the full six-stage pipeline,
the concept list per stage, the papers in reading order, and the open questions to
resolve before building.

## Timeline

| Round | Due | What |
|---|---|---|
| Registration | 29 Sept 2026, 18:30 | done |
| **Round 1** | **31 Oct** | 3D reconstruction + free roaming. Abstract, live viewer link, code repo, 3–5 min video. |
| Round 1 results | 10 Nov | |
| Round 2 | 30 Nov | POI tagging, search indexing, obstacle-aware pathfinding |
| Round 3 | 16–17 Dec | On-site capture at IIT Bombay + live pitch and Q&A |

Prize ₹1,50,000. Team up to 4. Cross-institution teams allowed.

## The pipeline

```
phone video → pose estimation → 3D reconstruction → browser viewer
                                                    ↓
                            POI search  ←───────────┤
                                                    ↓
                                    collision-free pathfinding
```

## Status

- [x] Registered
- [ ] Confirm eligibility (student-ID condition) — **blocking**
- [ ] Get the official capture SOP from techfest.org
- [ ] Run 3DGS on a public scene to validate the toolchain
- [ ] Load splats in a browser (`@mkkellogg/gaussian-splats-3d`) — highest-risk unknown
- [ ] Scan and reconstruct one real room
- [ ] Ship viewer + roaming video + abstract + repo

## The two genuinely new areas

Everything else maps onto existing ROS 2 / Isaac Sim / OpenCV experience:

1. **NeRF / 3D Gaussian Splatting** — the math and training, Stage 2
2. **Browser-based 3D rendering** — Three.js + WebGL2, Stage 3

Stage 5 (pathfinding) is Nav2, which is already known territory.

## Note on sourcing

Requirements were reconstructed from two independent third-party listings of the official
page. `techfest.org/competitions/nammaspace` returned 403/timeout to every automated
fetch attempt, so **verify the SOP, abstract format, and eligibility directly on the
official page** before relying on any date or requirement here.
