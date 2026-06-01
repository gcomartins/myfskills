# How Lusion / Active Theory-grade sites are built

A field guide to the techniques behind award-winning WebGL studio sites
(Lusion, Active Theory, Resn, Unseen). You can't see their minified source, but
this is the standard toolkit. Reach for the simplest technique that gets the look.

## 1. The render layer — WebGL, not DOM
The "heavy" visuals are a `<canvas>` (three.js / r3f), not CSS. HTML exists for
crisp text + layout. Map each idea to r3f via `references/r3f-recipes.md`.

## 2. Shaders (GLSL) — the look
- **Vertex displacement** with simplex/Perlin noise → organic blobs, ripples.
  (`MeshDistortMaterial` is this, prepackaged.)
- **Fragment shaders** → fresnel rims, iridescence, gradients, refraction.
- **Raymarching / SDF** → metaball/liquid shapes rendered entirely in a fragment
  shader on a fullscreen quad (no geometry). High craft; write a raw `ShaderMaterial`.
- Reach for `shaderMaterial` (drei) to wire `uTime`/`uMouse`/`uScroll` uniforms.

## 3. Simulation on the GPU (GPGPU)
Particles/fluids you can't do on CPU: store positions/velocities in a **floating-
point texture (FBO)**, a shader updates them each frame, read back as a texture for
rendering (FBO **ping-pong**). **Curl noise** drives flow fields. This is how you
get tens of thousands of particles at 60fps. (For a few hundred, CPU update is
fine — see recipe #3.)

## 4. Interaction = uniforms + lerp
- Pointer → world coords (raycast / unproject) → `uMouse` uniform; objects/particles
  attract or repel; distortion scales with **cursor velocity**.
- **Everything lerped** (`v += (target-v)*k`) — frame-rate-independent smoothing is
  THE "buttery" signature.

## 5. Scroll = a virtual value that drives everything
- A **custom smooth/virtual scroll** (Lenis is the open-source version): intercept
  native scroll, animate a virtual value, drive camera/objects/timelines from it.
- Long scroll ranges mapped to scene transitions (scrub), pinned sections.

## 6. DOM-synced WebGL planes (the image-hover trick)
The signature "images that distort/RGB-shift on hover":
1. Lay out images as normal DOM elements (for layout + a11y), often hidden/low-opacity.
2. Each frame, read each element's `getBoundingClientRect()` and **position a WebGL
   plane over it** (convert screen px → camera space).
3. The image is a **texture on a shader plane**; hover/scroll feed uniforms that
   warp UVs (ripple, RGB split, zoom). CSS can't do this; WebGL can.
This keeps text crisp in DOM while imagery gets shader effects. (Active Theory's
"hydra"/Lusion pipelines are variants of this.)

## 7. Orchestration & type
- **GSAP** timelines for the intro/preloader and staggered reveals, with expo/power
  easing. (In React you can also use framer-motion for DOM — see awesome-react-animations.)
- **Text**: split to chars/lines, masked + staggered (kinetic type); sometimes
  rendered in WebGL via **MSDF** font atlases for warping effects.

## 8. Preloader
Preload textures/models/shaders; a **0→100 counter** reflects progress; the intro
animation is gated on completion. (Drive the counter with rAF + a hard cap so it
never stalls in a background tab — see recipe #4.)

## 9. Page transitions (continuous canvas)
SPA routing where the **canvas persists across routes**: DOM swaps, WebGL scenes
crossfade/morph — no reload, one continuous experience (custom, Barba-like). In
React: keep the `<Canvas>` mounted above the router; use View Transitions for DOM.

## 10. Performance & fallbacks (always)
One rAF loop; cap DPR; instance; render-on-demand where possible; **reduced-motion
+ no-WebGL static fallback**; lighter scenes on mobile (fewer particles, drop
transmission/DOF). Transmission/refraction and DOF are the expensive effects.

## Which technique for which look
| Want | Reach for |
|---|---|
| Glossy glass orb | `MeshTransmissionMaterial` + `Environment` + ChromaticAberration |
| Liquid chrome blob | `MeshDistortMaterial` metalness 1 + colorful `Environment` |
| Floating dust / sparks | `Points`/`Sparkles` + flow + mouse repulsion (recipe #3) |
| 50k+ particles / fluid | GPGPU FBO ping-pong + curl noise |
| Metaball / liquid shape | raymarched SDF fragment shader |
| Images that warp on hover | DOM-synced WebGL planes + UV shader (#6) |
| "Whole site feels 3D" | persistent canvas + virtual scroll + scene transitions |

Start simple (drei materials + postprocessing get you 80% of the Lusion look with
near-zero shader risk). Escalate to custom GLSL / GPGPU only when the concept
demands it — and always keep the 60fps + fallback discipline.
