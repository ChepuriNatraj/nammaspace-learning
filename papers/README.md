# Papers, in the order worth reading

## Must read
1. **Kerbl et al. 2023** — *3D Gaussian Splatting for Real-Time Radiance Field Rendering*
   (SIGGRAPH 2023). https://repo-sam.inria.fr/fungraph/3d-gaussian-splatting/
   Read this first. It is short and it is the technique everyone uses.

2. **A Gaussian Splatting Primer** (2024). https://github.com/AlexWortega/gaussian-splatting-primer
   Best single overview — math *and* working code. Start here if Kerbl is dense.

## Depth
3. **Mildenhall et al. 2020** — NeRF. Original. You don't implement it, you should
   understand why 3DGS replaced it for this use case.

4. **Barron et al. 2023** — *3D Gaussian Splatting as Markov Chain Monte Carlo*.
   Why 3DGS converges. The clearest theoretical framing.

5. **Szymanowicz et al. 2023** — *Deblur-GS: Neural Splatting for Motion Deblurring*.
   Indoor phone captures are often blurry. **This is a genuine open problem and a
   plausible differentiator in your abstract.**

## Positioning / indoor specifics
6. **Szymanowicz et al.** — *DrivingGaussian* (composite scene representation)
7. Look for: *Instruct-NeRF2NeRF*, *Splatfacto*, *MonoGS* — recent fast implementations

## Capture side
8. **OpenVINS** (Patron-Perez et al.) — VIO that's practical to run on a phone
9. **SuperPoint** (DeTone et al. 2018) and **LightGlue** (Lindenberger et al. 2023) —
   the modern learned feature pipeline
10. **SuperGlue** (Sarlin et al. 2020) — matcher; LightGlue is its faster successor

## Navigation
11. ROS 2 Nav2 docs — `nav2_slam_toolbox`, costmap layers, planners
    https://docs.nav2.org

---

**Note:** add papers here as you read them, with one line on *why it matters for this
competition*. A reading list with notes is worth far more than a bare list.
