# View Transitions — shared-element "enter the card" morph

The most impressive portfolio/case-study move — clicking a card and having it
**grow into the detail page's hero**, then reverse on back — is not a hand-rolled
FLIP animation. It's React's `<ViewTransition>` riding the browser's native View
Transitions API. The browser snapshots the old and new DOM, finds elements that
share a `name`, and animates between their size/position for you. One object
persisting across a route cut reads as "same thing, going deeper" — exactly the
feeling you want.

This is verified against **Next.js 16 (App Router) + React 19**. It is the right
tool when motion crosses a route boundary; for in-tree morphs (a card → a modal
on the same page) framer-motion's `layoutId` is simpler.

## Setup (three things, or it silently no-ops)

1. **Enable the Next integration** so route navigations become the transitions
   that drive `<ViewTransition>`:

```ts
// next.config.ts
const nextConfig = { experimental: { viewTransition: true } }
```

2. **Import works, but the types don't ship yet.** Next vendors an experimental
   React build that *does* export `ViewTransition` / `addTransitionType` (no
   `unstable_` prefix at runtime), but stable `@types/react` has no such type, so
   `import { ViewTransition } from 'react'` red-squiggles and `unstable_*` is
   `undefined` at runtime. Bridge it once with a tiny typed wrapper:

```tsx
// lib/view-transition.tsx  ('use client')
import * as React from 'react'
type Named<T extends string> = T | Record<string, T>
export type ViewTransitionProps = {
  name?: string; children: React.ReactNode
  enter?: Named<string>; exit?: Named<string>; share?: Named<string>; default?: string
}
const R = React as any // eslint-disable-line @typescript-eslint/no-explicit-any
export const ViewTransition = R.ViewTransition as React.FC<ViewTransitionProps>
export const addTransitionType = R.addTransitionType as (t: string) => void
```

3. **Navigate with `<Link>` / `useRouter`** (App Router). Their navigations are
   React transitions, so the morph activates automatically — `setState` alone
   does *not* trigger it.

## The morph: same `name` on both pages

```tsx
// list card (old DOM)
<Link href={`/work/${p.slug}`} transitionTypes={['nav-forward']}>
  <ViewTransition name={`work-${p.slug}`}>
    <div className="card">{/* image + overlays */}</div>
  </ViewTransition>
</Link>

// detail hero (new DOM) — identical name
<ViewTransition name={`work-${p.slug}`}>
  <div className="hero">{/* same image, full-bleed */}</div>
</ViewTransition>
```

That's the whole effect. The named element is lifted out of the page snapshot
into its own group and morphs (card rect → hero rect, interpolating border-radius
too), while the rest of the page can slide. **Back navigation reverses it for
free** as long as both pages still render the matching name.

### Chain it
Give a "next project" card on the detail page `name={`work-${next.slug}`}` and it
morphs straight into the next detail hero — an endless, continuous walk through
the work with no hard cuts.

## Directional page slides (the non-morphing remainder)

Tag links with a transition type, then key CSS off `:active-view-transition-type`.
The morph element is exempt (it's its own group), so only the page body slides:

```tsx
<Link href={`/work/${slug}`} transitionTypes={['nav-forward']} />
<Link href="/"               transitionTypes={['nav-back']} />
```

```css
html:active-view-transition-type(nav-forward)::view-transition-old(root){
  animation: vt-fade .26s ease both reverse, vt-slide-left .5s cubic-bezier(.16,1,.3,1) both;
}
html:active-view-transition-type(nav-forward)::view-transition-new(root){
  animation: vt-fade .4s ease .12s both, vt-slide-right-in .5s cubic-bezier(.16,1,.3,1) both;
}
/* nav-back mirrors the offsets */
@keyframes vt-slide-left      { to   { transform: translateX(-7vw) } }
@keyframes vt-slide-right-in  { from { transform: translateX( 7vw) } }
@keyframes vt-fade { from { opacity:0; filter:blur(4px) } to { opacity:1; filter:blur(0) } }
```

Browser-initiated back (button/swipe) carries no type, so the slide is skipped —
but the shared-element morph still plays. That's the right default.

## Polish & correctness

- **Hide interpolation with a blur keyframe** mid-morph — the cross-fade between
  two differently-sized images can shimmer; a few px of blur at ~35% covers it:
  `::view-transition-image-pair(*){ animation-name: vt-morph-blur } @keyframes vt-morph-blur{ 35%{ filter: blur(4px) } }`.
- **Duration**: ~0.5–0.62s with an expo-out ease reads as "one object expanding,"
  fast enough to feel direct. Set it on `::view-transition-group(*)`.
- **A name must be unique among *currently rendered* elements.** A list where many
  cards each carry a unique `work-${slug}` is fine. Don't render the same name
  twice at once.
- **Reduced motion**: collapse it — `@media (prefers-reduced-motion: reduce){
  ::view-transition-group(*),::view-transition-old(*),::view-transition-new(*){
  animation-duration:.001ms !important } }`. Content still swaps instantly (the
  browser's no-transition default), which is correct.
- **Persistent WebGL caveat**: a `position:fixed` `<Canvas>` that lives only on
  the home route unmounts on navigation and re-inits on return (a brief cost/
  flash). If that matters, host the canvas in a layout that persists across the
  routes instead of in the page.

## Honest verification (this bit me)

Don't trust an instant `window.scrollTo(...)` / `scrollIntoView()` to test
`whileInView` reveals on the destination page: teleporting *past* an element can
skip the IntersectionObserver "intersecting" tick entirely, leaving reveals stuck
at their initial (hidden/clipped) state — a **test artifact**, not a user bug.
Real users (and Lenis) scroll smoothly through, firing the observer. Verify by
actually scrolling (wheel ticks), and confirm the morph itself in a real browser
— headless/preview environments often don't run View Transitions at all.
