# Animation Performance — the jank catalog

Every entry: the symptom, *why* it drops frames, and the fix that keeps the
motion. These are the things that make a React animation stutter.

## 1. `setState` on every animation frame — the React-specific killer

**Symptom:** A custom cursor, tilt card, magnetic button, count-up, or
scroll-driven value that updates from `mousemove`/`scroll`/an animation
`onUpdate`, stored in `useState`.
**Why:** Each `setState` re-runs render + reconciliation for that subtree, up to
60×/sec. With several such components mounted, the main thread never rests and
the compositor starves.

```tsx
// BEFORE — re-renders the card every mousemove
const [glow, setGlow] = useState({ x: 50, y: 50 })
const onMove = e => { …; setGlow({ x: px*100, y: py*100 }) }
<div style={{ background:`radial-gradient(circle at ${glow.x}% ${glow.y}% …)` }} />

// AFTER — write to the DOM node via ref; zero re-renders
const glowRef = useRef(null)
const onMove = e => { …; glowRef.current.style.background =
  `radial-gradient(circle at ${px*100}% ${py*100}% …)` }
<div ref={glowRef} />
```

For values that drive `transform`/`opacity`, prefer a **motion value** so
framer-motion updates the element off the main thread:

```tsx
const x = useMotionValue(0)
const sx = useSpring(x, { stiffness: 250, damping: 18 })
const onMove = e => x.set(/* … */)        // no render; spring runs on its own loop
<m.div style={{ x: sx }} />
```

Count-ups: animate with `animate()` and write `node.textContent` in `onUpdate`
instead of `setState` — the number changes without re-rendering siblings.

## 2. Animating an expensive `filter` (blur) instead of a transform

**Symptom:** A glowing orb / gradient with `blur(120px)` that pulses (animates
`scale`/`opacity` forever).
**Why:** Blur cost scales with radius² and doubles at DPR 2. Animating a heavily
blurred element can force re-rasterization of the blur each frame.

```html
<!-- BEFORE: huge animated blur, re-rasterized per frame -->
<m.div class="blur-[130px]" animate={{ scale:[1,1.25,1] }} … />
<!-- AFTER: smaller radius + promote, so scale is GPU-only on a cached texture -->
<m.div class="blur-[64px] [will-change:transform]" animate={{ scale:[1,1.25,1] }} … />
```

## 3. Animating a full-screen `mix-blend-mode` overlay

**Symptom:** Animated film grain / noise covering the viewport for a shimmer.
**Why:** A blend mode composites the overlay against everything beneath it,
pulling the whole page out of compositor-only into a repaint every frame.
**Fix:** Drop the blend mode; isolate the layer (`transform: translateZ(0)`,
`will-change: transform`) and animate only `transform`. Lower opacity to
compensate for the lost "overlay" punch. An oversized layer (200%) hides the
translate's edges.

## 4. Animating `backdrop-filter` (or animating *over* one)

**Symptom:** A frosted element that moves, or content scrolling behind a fixed
frosted bar.
**Why:** `backdrop-filter` re-samples and re-blurs the backdrop every frame it
or the content behind it moves — brutal during scroll, multiplied per element.
**Fix:** For dark themes, a semi-opaque solid fill is visually identical at zero
cost. Reserve real `backdrop-filter` for something static over a colorful photo.

## 5. Animating layout-triggering properties

**Symptom:** Animating `width`, `height`, `top`, `left`, `margin`, or animating
`height: auto` on expand/collapse.
**Why:** Each frame triggers layout (reflow) of the element and often its
siblings — the most expensive pipeline stage.
**Fix:** Animate `transform: scale()`/`translate()` instead. For
expand/collapse, framer-motion's `layout` / `AnimatePresence` with `height:auto`
is acceptable because it measures once and animates with transforms under the
hood — but a hand-rolled `style={{ height }}` tween is not.

## 6. Non-passive, non-coalesced listeners driving animation

**Symptom:** `mousemove`/`scroll` handlers doing DOM work (`closest()`, layout
reads) on every event to drive an animation.
**Why:** Without `{ passive: true }` the handler blocks scrolling; firing
hundreds of times/sec compounds it.
**Fix:** Register `{ passive: true }` and coalesce to one run per frame:

```js
let target = null, queued = false
const flush = () => { queued = false; if (target) setHovering(!!target.closest('a,button')) }
window.addEventListener('mousemove', e => {
  target = e.target
  if (!queued) { queued = true; requestAnimationFrame(flush) }
}, { passive: true })
```

## 7. Too many simultaneous rAF loops

**Symptom:** A smooth-scroll lib + several hand-rolled `requestAnimationFrame`
loops + spring updates all running at once.
**Why:** Each loop is main-thread work fighting for the frame budget.
**Fix:** Let framer-motion batch its animations into its single internal loop;
avoid hand-rolled rAF where a motion value/spring would do. Keep at most one
extra loop (e.g. the smooth-scroll lib).

## Measuring honestly

- **Headless/preview browsers can lie.** Electron-based capture and some CI
  Chromes skip `backdrop-filter`/`mix-blend-mode`/heavy filters —
  `CSS.supports()` says `true` but `getComputedStyle(el).backdropFilter` is
  `none`, and rAF-sampled FPS reads uncapped/high while real hardware janks.
  Verify the effect actually renders before trusting a number.
- **The real check** is a DevTools Performance trace recorded over the
  interaction (look for long tasks and full-page repaints) — or the user feeling
  it on their device. Say which you did.

## Quick audit grep

```bash
grep -rnE "blur-\[[0-9]{3}|mix-blend|backdrop|repeat: Infinity" src --include="*.tsx" --include="*.css"
# per-frame setState: setState inside mousemove/scroll/onUpdate handlers
grep -rn "useScroll|useTransform" src --include="*.tsx"   # scroll-linked work
```
