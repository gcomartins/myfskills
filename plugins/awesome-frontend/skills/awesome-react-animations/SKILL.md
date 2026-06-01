---
name: awesome-react-animations
description: >
  Best practices for animations in React that stay buttery-smooth (60fps, no
  jank) — because motion and performance have to coexist: a beautiful animation
  that stutters is worse than none. Use this whenever you add, review, or fix
  animation in a React/Next app: framer-motion / `motion` / `m`, springs,
  variants, `AnimatePresence`, layout animations, scroll reveals, parallax,
  gesture/hover micro-interactions, page or route transitions, staggered or
  kinetic text, count-ups, custom cursors, marquees, View Transitions. Trigger
  it even when the user just says "add some animation", "make it animated /
  interactive / alive", "animate this", "add a transition", or complains that an
  animation feels janky / laggy / stuttery / "travado". Core promise: keep React
  out of the per-frame loop and animate on the GPU compositor — every effect the
  designer wanted, paid for in the right place.
---

# Awesome React Animations — smooth by construction

Animation is where an interface earns the word "quality." But the same motion
that delights at 60fps feels *broken* at 30. So in React, animation and
performance are not two topics — they're one. Almost every janky React
animation traces to the same root cause: **React is doing work on a frame it
shouldn't be on.** Get React out of the per-frame loop and push the motion onto
the GPU compositor, and smoothness stops being something you tune after the
fact — it's structural.

## The mental model: what runs each frame

Per frame the browser does: **JS → Style → Layout → Paint → Composite.** An
animation is cheap only if it stays at the bottom. Two properties animate on the
compositor alone, off the main thread: **`transform` and `opacity`.** Everything
else (width, top, box-shadow, filter, background, color) risks layout or paint
*every frame*. And in React there's a layer above all of this: a component that
calls `setState` 60×/sec re-runs render + reconciliation before the browser even
starts that pipeline.

So there are two golden questions for any animation:
1. **Is it animating `transform`/`opacity`?** If not, find a way to make it so.
2. **Is React re-rendering every frame to drive it?** If so, move the per-frame
   value out of React.

## The golden rules

**1. Per-frame values never live in `useState`.** Cursor position, tilt angle,
scroll progress, a spotlight's coordinates, a counting number — these change
every frame. Storing them in state means a render every frame. Use a motion
value (`useMotionValue`) which updates the DOM via the compositor without
re-rendering, or write straight to the node through a ref. This single rule
prevents most React animation jank. (See `references/animation-performance.md`.)

**2. Animate `transform` and `opacity`; fake the rest.** Don't animate a blur's
radius — pre-render the blur and animate its `scale` with `will-change:transform`
so the GPU reuses one cached texture. Don't animate `width`/`height` — use
`scale`. Don't animate `top`/`left` — use `x`/`y` (translate).

**3. Reach for the library's primitives, not your own rAF + state.**
framer-motion's `useSpring`, `useTransform`, `useScroll`, `animate()`, and the
`whileHover`/`whileTap`/`drag` props all run their loops *outside* React render.
Hand-rolling `requestAnimationFrame` + `setState` is the thing that's slow. (See
`references/framer-motion-patterns.md`.)

**4. Ship less animation JS: `LazyMotion` + `m`.** Use `<LazyMotion features=…>`
once at the root and the lightweight `m` component everywhere (`m.div`, not
`motion.div`) to defer ~30kb until needed. Big bundles delay hydration, which
*is* startup jank.

**5. Promote continuously-animated elements deliberately.**
`will-change: transform` (or `translateZ(0)`) gives a forever-looping element its
own compositor layer so it doesn't drag neighbors into repaints. Use it on the
few things that actually animate non-stop — not everywhere; each layer costs GPU
memory.

**6. Respect `prefers-reduced-motion`.** Gate continuous/large motion and
disable smooth-scroll for users who asked for less. It's accessibility *and* a
free perf win on weak hardware. The canonical *global* gate is
`<MotionConfig reducedMotion="user">` at the root — it snaps every `m`
component's transforms for you, so you don't sprinkle `matchMedia` checks through
every component. Still disable Lenis/smooth-scroll and any custom cursor
explicitly. Avoid the `useEffect` + `matchMedia` + `setState` capability-gate
pattern as your default: Next 16's `react-hooks/set-state-in-effect` lint rule
flags a synchronous `setState` inside an effect as an **error** (react-doctor will
fail on it). If you must gate in JS, prefer `useSyncExternalStore` (SSR-safe, no
effect-setState) or defer the set with `queueMicrotask`/`setTimeout(…,0)`.

**7. On-mount / above-the-fold reveals: use a CSS keyframe, not framer.** This is
a silent-content-loss footgun, not just jank. framer's `whileInView` /
`animate`-on-mount on per-word masked spans can *intermittently never fire* after
SSR hydration — the IntersectionObserver tick is missed or the mount animation
races React, and the element stays stuck at its `from` state (`translateY(110%)`,
`opacity:0`) — invisible. For reveals that play once on load, animate with a CSS
`@keyframes` rise/fade instead: it runs on paint, can't race hydration, stays
compositor-only (`transform`/`opacity`), and honors reduced-motion via a media
query. Reserve framer `whileInView` for genuinely *scroll-in* reveals below the
fold. If a screenshot shows blank text the DOM proves is painted, suspect a
promoted-layer capture artifact first — but a stuck clipped transform is also
real; confirm via DOM inspection either way.

## Choosing the tool

- **CSS transition/animation** — hover states, simple enter/exit, looping
  ambient motion (a pulsing glow, a marquee). Cheapest; no JS. Keep it to
  `transform`/`opacity`.
- **framer-motion (`m`)** — anything stateful or orchestrated: enter/exit of
  lists (`AnimatePresence`), shared-layout transitions (`layout`), springs,
  gesture-driven motion, scroll-linked parallax, staggered reveals. The default
  for "real" UI animation in React.
- **React View Transitions** (`<ViewTransition>`, `addTransitionType`) — route and
  large UI-state changes where you want the browser to crossfade/morph between two
  DOM states. The standout is the **cross-route shared-element morph**: give a list
  card and the detail-page hero the same `name` and clicking "enters the card" — it
  grows into the hero, and back reverses it, for free. Far less code than
  orchestrating exit+enter across a route with `AnimatePresence`. (Setup, the typed
  wrapper for the still-missing types, directional slides, and the gotchas:
  `references/view-transitions.md`.)
- **WebGL / 3D** (real geometry, shaders, glass/particles, immersive heroes) — out
  of scope here; this skill is DOM/compositor motion. For react-three-fiber, drei,
  postprocessing, and shader work, use the **awesome-webgl** skill (it keeps the
  same 60fps + reduced-motion discipline).

Match the tool to the job; don't pull in framer-motion to fade one button CSS
could handle, and don't hand-animate a route change View Transitions would do
for free.

## Deploying animation in React/Next without tax

Animated components are Client Components. That's fine — but don't let one
animated widget turn a whole page into `'use client'` and re-hydrate everything.
Keep pages as Server Components and isolate motion in **small client islands**:
one reusable `<FadeIn>` reveal wrapper, one `<Carousel>`, one `<ContactChat>`.
Less client JS = faster hydration = the first interaction doesn't stutter. (See
`references/scroll-and-reveal.md` for the reveal-island pattern.)

## Workflow when adding or fixing animation

1. **Pick the cheapest tool** that achieves the motion (CSS → `m` → View
   Transitions).
2. **Keep per-frame values out of React** — motion values / refs, never state.
3. **Stay on `transform`/`opacity`**; if you're tempted to animate something
   else, find the transform equivalent.
4. **Verify honestly.** Build/typecheck. Confirm the animation actually renders
   in your measurement environment before quoting FPS — some headless/preview
   browsers silently skip `backdrop-filter`/`mix-blend-mode`/filters, so an FPS
   reading there can look perfect while real hardware chugs. Record a DevTools
   Performance trace over the interaction, or say plainly the user should feel it
   on their machine. And don't test scroll reveals with an instant
   `scrollTo`/`scrollIntoView` — teleporting past an element skips the
   IntersectionObserver tick and leaves `whileInView` stuck hidden; that's a test
   artifact, not a user bug. Scroll for real (wheel ticks) to verify.
5. **Preserve the intent.** If smoothing it changed how it looks, you went too
   far — find a cheaper path to the *same* motion, don't water it down.

## Reference files

- `references/animation-performance.md` — the jank catalog: per-frame `setState`,
  animated blur/`backdrop-filter`/`mix-blend-mode`, layout-triggering properties,
  passive listeners, measuring honestly. Before/after for each.
- `references/framer-motion-patterns.md` — motion values vs state, springs,
  `useTransform`, `AnimatePresence`, `layout` animations, variants + stagger,
  gestures, `LazyMotion`/`m`, imperative `animate()`.
- `references/scroll-and-reveal.md` — `whileInView`/`useInView` reveals, scroll-
  linked parallax with `useScroll`/`useTransform`, the server-page + reveal-island
  pattern, smooth-scroll tradeoffs, `content-visibility` interplay.
- `references/view-transitions.md` — the cross-route shared-element morph ("enter
  the card") with React `<ViewTransition>` + Next 16: setup, the typed wrapper for
  the missing types, chaining morphs, directional `nav-forward`/`nav-back` slides,
  blur polish, reduced-motion, and the honest-verification gotchas.

## One line

In React, "make it smooth" and "make it animated" are the same instruction:
keep React off the per-frame path, animate on the compositor, and the motion is
fast because of how it's built — not because you optimized it later.
