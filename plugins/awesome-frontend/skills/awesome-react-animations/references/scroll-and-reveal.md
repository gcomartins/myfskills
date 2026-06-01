# Scroll & Reveal Animations

Scroll-triggered and scroll-linked motion are where performance and animation
most obviously collide — scroll is a hot path, so anything you hang on it must
be cheap.

## Reveal on scroll — `whileInView` / `useInView`

The cheapest reveal: animate once when an element enters the viewport. Driven by
an IntersectionObserver, not by scroll events, so it costs nothing while
scrolling.

```tsx
<m.div
  initial={{ opacity: 0, y: 24 }}
  whileInView={{ opacity: 1, y: 0 }}
  viewport={{ once: true, margin: '-8% 0px' }}   // fire slightly before fully in view
  transition={{ duration: 0.7, ease: [0.16, 1, 0.3, 1] }}
/>
```

`once: true` matters — without it the element re-animates every time it
re-enters, which is both distracting and wasteful. For finer control,
`useInView(ref, { once: true, margin })` returns a boolean you can branch on.

> **Two reveal gotchas, both real bugs I've hit:**
> - **Above-the-fold content shouldn't use `whileInView`.** A hero already in view
>   on mount may never get an "entering" IntersectionObserver tick, so its words
>   stay stuck at `y:115%` (clipped) and the headline is invisible. Drive
>   above-the-fold reveals with `animate` (play on mount), and reserve
>   `whileInView` for things the user scrolls *to*.
> - **Don't verify reveals with an instant `scrollTo`/`scrollIntoView`.**
>   Teleporting past an element skips the intersecting tick and leaves it stuck at
>   its initial state — a *test artifact*, not a user bug (real users + Lenis
>   scroll smoothly through and fire the observer). Scroll with real wheel ticks
>   when checking.

## The reveal-island pattern (keep pages on the server)

A reveal needs a Client Component, but don't make a whole page `'use client'`
for it. Build **one** reusable client wrapper and keep the page a Server
Component — only the wrappers hydrate, not the page's content:

```tsx
// FadeIn.tsx ('use client')
export function FadeIn({ children, className, delay = 0, x = 0, y = 24 }) {
  return (
    <m.div
      initial={{ opacity: 0, x, y }}
      whileInView={{ opacity: 1, x: 0, y: 0 }}
      viewport={{ once: true, margin: '-8% 0px' }}
      transition={{ duration: 0.7, delay, ease: [0.16, 1, 0.3, 1] }}
      className={className}
    >{children}</m.div>
  )
}
```

```tsx
// page.tsx — Server Component, ships content as HTML
export default function About() {
  return (
    <section>
      <FadeIn><h1>…</h1></FadeIn>
      {items.map((it, i) => <FadeIn key={it.id} delay={i * 0.1}>…</FadeIn>)}
    </section>
  )
}
```

Less client JS = faster hydration = the first scroll/interaction doesn't stutter.

## Scroll-linked motion — `useScroll` + `useTransform`

For parallax and progress-driven effects, map scroll progress to a motion value.
Because it's motion-value-based, it updates the element off the render path.

```tsx
const ref = useRef(null)
const { scrollYProgress } = useScroll({ target: ref, offset: ['start end', 'end start'] })
const y = useTransform(scrollYProgress, [0, 1], [80, -80])   // parallax
const opacity = useTransform(scrollYProgress, [0, 0.7], [1, 0])
<m.div ref={ref} style={{ y, opacity }} />
```

Keep the mapped outputs to `transform`/`opacity`. Don't drive `top`, `height`,
or `filter` from scroll — that's layout/paint on every scroll frame. Limit the
number of independent `useScroll` instances on one page.

## Smooth-scroll libraries (Lenis, Locomotive)

They make scroll feel cinematic but can *introduce* the lag they're meant to
remove if tuned heavily.

```js
// Heavy: a long fixed duration is perceptible lag between gesture and screen
new Lenis({ duration: 1.1 })
// Better: lerp is frame-rate independent and responsive; keep touch native
new Lenis({ lerp: 0.12, smoothWheel: true, syncTouch: false })
```

- `lerp ≈ 0.1` follows closely; lower = floatier/laggier.
- Smoothed **touch** is the jankiest part — leave it native (`syncTouch:false`).
- Always gate behind `prefers-reduced-motion`; the truest "native-smooth" option
  is no JS smooth-scroll at all.
- Run its rAF loop once; don't stack it with other hand-rolled loops.

## `content-visibility` and offscreen sections

`content-visibility: auto` (with `contain-intrinsic-size: auto <h>`) lets the
browser skip layout+paint for below-the-fold sections until they're near the
viewport — cheaper scrolling on long pages. It composes fine with `whileInView`
(which uses IntersectionObserver). Don't put it on the first/hero section, and
use `auto` intrinsic size so the scrollbar doesn't jump after first render.

## Page / route transitions — prefer View Transitions

For animating between routes or large UI states, React's View Transitions
(`<ViewTransition>`, `addTransitionType`, `startViewTransition`) let the browser
crossfade/morph the old and new DOM without you animating each element. It's
usually smoother and far less code than orchestrating exit+enter by hand with
`AnimatePresence` across a route boundary. Reach for it for directional
back/forward nav, shared-element route transitions, and list reorders.
