---
name: awesome-webgl
description: >
  Build beautiful, performant interactive 3D / WebGL on the web with React —
  react-three-fiber (r3f), drei, and postprocessing. Use whenever a project needs
  real 3D or shader-driven motion: an immersive/awwwards hero, a glass / chrome /
  iridescent object, distorted blobs, particle fields, fluid, cursor-reactive or
  scroll-driven 3D scenes, model viewers, shader backgrounds, or "make it look
  like Lusion / Active Theory / a WebGL studio site". Trigger on "WebGL",
  "three.js", "react-three-fiber / r3f", "3D on the web", "shader", "GLSL",
  "interactive 3D", "particles", "immersive experience". This is the
  IMPLEMENTATION skill for heavy 3D — pair it with awesome-react-animations
  (DOM/compositor motion) and awesome-frontend-design / the style + portfolio
  lenses (look & feel). Core promise: real 3D that still hits 60fps — cap DPR,
  instance, gate on reduced-motion + capability, and keep React off the per-frame
  path.
---

# Awesome WebGL — interactive 3D on the web, done right

WebGL is how you get the "how is this a website?" reaction — glass that refracts,
blobs that follow the cursor, particle fields, scroll-driven worlds. The trap is
that it's easy to make something gorgeous that runs at 20fps or crashes mobile.
This skill is the playbook for **real 3D that stays smooth**, built on
**react-three-fiber + drei + postprocessing**. It's grounded in a real,
deployed flagship (an immersive Lusion-style portfolio) — concrete, verified code
lives in `references/r3f-recipes.md`; the "how the awwwards studios do it"
catalog is in `references/lusion-techniques.md`.

## The stack

- **three.js** — the renderer. **@react-three/fiber** — React renderer for three
  (declarative scene as JSX, `useFrame` loop). **@react-three/drei** — helpers
  (materials, `Environment`, `Float`, controls, loaders). **@react-three/postprocessing**
  — `EffectComposer` + `Bloom`, `ChromaticAberration`, `DepthOfField`, etc.
- Pair with **lenis** (smooth scroll) and **framer-motion** (DOM reveals) — see
  awesome-react-animations.

## Next.js / SSR integration (the #1 gotcha)

WebGL is client-only. `next/dynamic(..., { ssr: false })` **only works inside a
Client Component** — using it in a Server Component throws. So:

```tsx
// Experience.tsx  ('use client')  — the client island that owns the canvas
"use client";
import dynamic from "next/dynamic";
const Scene = dynamic(() => import("./Scene"), { ssr: false });
export default function Experience() {
  const [ok, setOk] = useState(false);
  useEffect(() => setOk(!matchMedia("(prefers-reduced-motion: reduce)").matches), []);
  return <div className="fixed inset-0 -z-0">{ok ? <Scene /> : <StaticFallback />}</div>;
}
```

Render the canvas as a fixed background layer; let opaque DOM sections scroll over
it. Always ship a **static fallback** (CSS gradient/image) for reduced-motion and
no-WebGL.

## The per-frame model (smoothness comes from here)

- Animate inside **`useFrame`**, not React state. **Never `setState` per frame.**
- **Lerp everything**: `v += (target - v) * k` — frame-rate-independent smoothing
  is the "buttery" feel. Drive rotation/scale/material props this way.
- Feed input as values, not renders: read `pointer` (NDC) and `window.scrollY`
  inside `useFrame`; lerp toward them. Mouse velocity → distortion strength.
- One render loop. Cap **`dpr={[1, 1.8]}`**. Use `frameloop="demand"` for static
  scenes; default `always` only when something animates every frame.

## Technique catalog (details + code in references)

- **Glass / refraction** — drei `MeshTransmissionMaterial` (transmission, ior,
  thickness, `chromaticAberration`, `distortion`) over a colorful `Environment`.
  The signature premium look. (Cost: it renders a buffer — keep `resolution`/
  `samples` modest: 256 / 4.)
- **Chrome / liquid metal** — `MeshDistortMaterial` (noise vertex displacement) +
  `metalness:1`, low roughness, a colorful `Environment` for iridescent reflections.
- **Particles** — `Points` (or `InstancedMesh`) with a flow field (curl/simplex
  noise) + cursor repulsion. CPU-updated for a few hundred; GPGPU (FBO ping-pong)
  for tens of thousands. Build the initial buffer in `useMemo` with a **seeded
  PRNG**, not `Math.random()` — Next 16's `react-hooks/purity` lint rule errors on
  impure calls during render (it fires inside `useMemo` too).
- **Postprocessing** — `Bloom` (high `luminanceThreshold` on light themes so it
  doesn't wash out), `ChromaticAberration`, `DepthOfField` for the "lens" feel.
- **Scroll-driven scenes** — map scroll progress to camera/object transforms;
  shrink/drift the hero object as you enter content; scrub timelines.
- **Custom GLSL** — `shaderMaterial` (drei) / raw `ShaderMaterial` with
  `uTime`/`uMouse` uniforms for fresnel, gradients, raymarched SDF/metaballs.
- **DOM-synced WebGL planes** (the awwwards image trick) — position WebGL planes
  over DOM elements via `getBoundingClientRect` each frame; images become textures
  on shader planes → hover distortion / RGB-shift that CSS can't do. See
  `lusion-techniques.md`.

## Performance & accessibility (non-negotiable)

- **Cap DPR** (`[1, 1.8]`), **instance** repeated meshes (`InstancedMesh`),
  reuse geometries/materials, dispose on unmount.
- **Reduced-motion + capability fallback**: static image/gradient instead of the
  canvas. Lighter scene (fewer particles, no transmission) on mobile.
- **Transmission/refraction and DOF are the expensive effects** — measure; lower
  resolution/samples; consider disabling on mobile.
- **Don't animate CSS `blur()`** on big layers (paint-heavy) — do depth in WebGL.
- **Lazy-load** the scene (dynamic import) so the 3D bundle doesn't block first
  paint; gate behind a preloader.

## Real gotchas (learned the hard way)

- `ssr: false` in a Server Component → error. Put the dynamic import in a Client
  Component.
- A **preloader counter on `setInterval` stalls in a background tab** (browsers
  throttle timers to ~1/s). Drive it with `requestAnimationFrame` + a hard
  `setTimeout` cap so it always reveals.
- framer-motion **`whileInView` may not fire for above-the-fold content** — use a
  mount/`animate` trigger for the hero, `whileInView` only for below-fold.
- drei material refs are impl types (e.g. `DistortMaterialImpl`) — type loosely
  (`useRef<any>`) when you set `.distort`/`.chromaticAberration` imperatively.
- `MeshTransmissionMaterial` needs an `Environment` to refract; without one it
  reads flat/black.
- A `position:fixed` `<Canvas>` that lives only on one route **unmounts and
  re-inits WebGL on navigation** (brief cost/flash). If you add route View
  Transitions, host the canvas in a layout that persists across the routes — or
  accept the re-init. (See awesome-react-animations `references/view-transitions.md`.)

## References
- `references/r3f-recipes.md` — copy-paste, verified snippets: the dynamic
  Experience wrapper, glass blob, cursor+scroll-reactive object, interactive
  particle field, robust preloader, Lenis smooth scroll + animated anchor links.
- `references/lusion-techniques.md` — how Lusion / Active Theory-grade sites are
  built (shaders, GPGPU, DOM-synced planes, virtual scroll, page transitions) and
  which technique to reach for.
- Pair with **awesome-react-animations** (DOM motion, 60fps discipline) and the
  **portfolio-web-design / awesome-frontend-design** skills (what to build & why).
