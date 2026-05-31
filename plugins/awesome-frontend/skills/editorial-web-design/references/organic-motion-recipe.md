# The Organic-Motion Recipe

What makes a site feel *organic* — alive, staged, authored — rather than a stack
of fade-ups. This is the motion *direction*: the sequence and feel. For making any
of it run at 60fps in React, follow the **awesome-react-animations** skill; this
file is about *what* to choreograph and *why*.

The reference stack (heyhoncho) was Webflow + GSAP/ScrollTrigger + Splitting.js +
Barba.js + Locomotive + PreloadJS. You don't need those exact tools — the table at
the end maps each idea to a modern React/Next equivalent.

## The seven ingredients

### 1. The intro preloader (earns the first reveal)
A counted **0→100** while real assets preload, with a line or two of text
animating in **char-by-char**, then a curtain wipe into the hero. It does two
jobs: hides loading so the first paint is instant *and* gives you a dramatic,
authored entrance. Keep it short (≈1–1.5s) and skip it on repeat visits / reduced
motion. This single touch separates "site" from "experience."

### 2. Smooth / inertial scroll (the weight)
Lerp-based smooth scroll gives the page momentum and flow — the core of the
"organic" feel. Tune it light (lerp ≈ 0.12), keep touch native, gate on
`prefers-reduced-motion`. Don't over-damp it into lag.

### 3. Kinetic type (split + stagger)
Split headings into **lines → words → chars** and stagger them in (mask each line
with `overflow:hidden`, translate from 110%→0). Honcho splits its gallery titles
*and* client names per project so each one assembles as it enters. This is the
highest-impact reveal you can do with type. Stagger ≈ 0.04–0.08s/char, expo ease.

### 4. Scroll-synced reveals & parallax (choreography)
Tie reveals and gentle parallax to scroll position so images, captions, and type
move in relationship — caption drifts slower than its image, a title scrubs as
the section passes. Reveal once (don't re-fire), use `transform`/`opacity` only.

### 5. Page transitions (continuity)
Replace hard reloads with a **cover/curtain or shared-element transition** so
moving between index and detail feels continuous. Honcho uses Barba; in React use
**View Transitions** (or AnimatePresence/layout). A detail page can land on a
centered "title-card" hero that then plays its own reveal.

The premium version is a **shared-element morph**: the caption you clicked
*persists across the navigation and becomes the detail hero's title* (Honcho
sweeps a red curtain up, swaps the URL with no reload, and the gallery caption
"MADE FOR CHICAGO." reflows into the centered title card). In React this is
exactly what `view-transition-name` on the shared element gives you — the same
node morphs from list to detail. It reads as continuity, not navigation.

Refinement worth stealing: **re-animate only what changes.** Honcho's caption
keeps the constant lines ("MADE FOR ALL.", the client) and char-shuffles *only*
the variable city line as projects scroll past. Animating the whole block every
time feels mechanical; animating only the delta feels alive.

### 6. Hover micro-reveals (care)
Image **clip/curtain reveals** (two stacked layers, `clip-path`/`scale` on hover),
link under-draws, magnetic buttons, an optional custom cursor. Small, consistent,
transform-based. They reward attention and signal craft.

### 7. Custom easing (the biggest tell)
Nothing uses the browser default `ease`. Organic motion lives on **expo/power
curves** — fast out, long settle. Reuse ONE signature curve everywhere, e.g.
`cubic-bezier(0.16, 1, 0.3, 1)` (expo-out), or a spring for interactive elements.
Consistent custom easing is what unifies the whole site's "feel".

## Choreography, not sprinkles
Script the timeline like a film: **load → reveal → scroll beats → transition.**
Decide what enters, in what order, with how much overlap. The difference between
"nice" and "organic" is *overlap and rhythm* — elements cascade with intentional
delays, not all at once with identical timing.

## Reference stack → modern React/Next translation

| Reference (heyhoncho) | What it does | Modern React/Next equivalent |
|---|---|---|
| Locomotive Scroll | smooth/inertial scroll | **Lenis** (lerp ~0.12, native touch) |
| Splitting.js + GSAP | char/line split + stagger | split helper + **framer-motion** variants (`staggerChildren`), `overflow:hidden` masks |
| GSAP ScrollTrigger | scroll reveals / parallax / scrub | **`whileInView`** + **`useScroll`/`useTransform`** |
| Barba.js | page transitions | **React View Transitions** (or AnimatePresence/`layout`) |
| PreloadJS + counter | asset preload + 0→100 intro | preload gate + counted intro + char-stagger reveal |
| GSAP eases (expo/power) | custom easing | `cubic-bezier(0.16,1,0.3,1)` / springs, reused everywhere |
| Webflow IX2 hovers | hover clip-reveals | two layers + `clip-path`/`scale` on hover (transform/opacity) |

## Restraint
Organic ≠ busy. Honcho is mostly *still* — vast calm space punctuated by a few
beautifully-timed moves. Choreograph a handful of moments with custom easing and
overlap; leave the rest quiet. And always: implement per awesome-react-animations
so the motion is smooth, reduced-motion-aware, and off the per-frame React path.
