---
name: editorial-web-design
description: >
  The EDITORIAL / magazine / lookbook design lens — loud, art-directed, "organic"
  sites that feel like a fashion or culture print spread brought to the web. Use
  when the user wants this look or names it: "editorial", "magazine", "lookbook",
  "fashion", "agency/studio portfolio", "awwwards-style", "make it feel like a
  print spread / art-directed / organic", or is designing a creative studio,
  production company, photographer, or brand showcase. This is a STYLE LENS — apply
  it on top of the awesome-frontend-design method, and implement its motion with
  awesome-react-animations. Anchored in a real teardown of heyhoncho.com. Core
  feel: huge type in tension, restrained bold palette, vast negative space,
  numbered indices, masthead-style credits, and choreographed motion with custom
  easing.
---

# Editorial / Magazine — the lens

Editorial web design treats the screen like a fashion-magazine or culture-print
spread: a strong art-directed point of view, enormous expressive type, generous
negative space, and motion choreographed like a title sequence. It's the opposite
of the even, tidy SaaS template — it's loud, confident, and *curated*.

Apply this lens **on top of** the `awesome-frontend-design` method (concept → type
→ color → composition → motion). This file supplies the editorial vocabulary; the
real worked example is in `references/case-study-heyhoncho.md` (with screenshots),
and the motion recipe in `references/organic-motion-recipe.md`.

## What makes it "editorial"

- **Type in tension, set HUGE.** Pair a display face *with attitude* (heavy,
  condensed, expressive grotesque or a high-drama serif) set enormous — page-filling
  titles — with a **refined counterpoint** (a high-contrast serif, often italic)
  for labels and credits. Mix the two even *within a line* for emphasis.
- **Masthead label/value.** Style metadata like a magazine masthead or film
  credits: *serif-italic descriptor* + GROTESQUE-CAPS VALUE ("*Client:* …",
  "*Photographer* …"). Reuse it everywhere for cohesion.
- **Numbered indices.** Number items (01, 02, 03…), treat a listing like a
  contents spread, pair each entry with a label/value caption.
- **Restrained, bold palette.** Often near-monochromatic and high-personality — a
  committed hue across a few values (a tinted canvas, a saturated mid, a dark
  text). Hard edges (`border-radius: 0`) read editorial; commit to a corner
  geometry. Avoid timid gray-on-white.
- **Vast negative space.** Float one image/video in a field of canvas; anchor a
  headline to an edge. The emptiness *is* the styling — it signals curation.
- **Imagery as plates, often video.** Treat media as framed plates with generous
  margins; autoplaying silent reels in place of stills add life. Hover
  clip/curtain reveals.
- **Choreographed "organic" motion.** An intro preloader, inertial smooth scroll,
  kinetic split-text reveals, scroll-synced captions, and continuous page
  transitions — all on **custom expo easing**. See the recipe.

## The signature moves (from the teardown)

- **Preloader that inverts the palette** + a 0→100 counter + char-split text, then
  a curtain wipe (sometimes an oversized greeting wordmark) into the content.
- **Sticky caption synced to a scrolling media column** — and re-animate *only the
  part that changes* (the variable line), keeping constants steady. Feels alive,
  not mechanical.
- **Shared-element page transition** — the caption you click persists and morphs
  into the detail page's centered title-card (curtain wipe, no hard reload). In
  React: `view-transition-name` on the shared text.

## Implementing on a free / Next.js stack

The teardown names commercial faces (FK Screamer, Heldane). Free substitutes that
hit the same "condensed shouting grotesque + high-contrast serif" tension:
- **Loud grotesque:** Archivo (has a `wdth` axis), Anton, Oswald, Bebas Neue.
- **Refined serif:** Fraunces (optical + soft axes), Playfair Display, Newsreader.

Two Next.js sharp edges this lens reliably hits:
- **`next/font/google` axes vs weight:** specifying `weight: [...]` *and*
  `axes: [...]` together is a hard build error ("Axes can only be defined … when
  the weight property is … `variable`"). When you want a width/optical axis (e.g.
  Archivo `wdth`), **omit `weight`** — the full variable range loads and CSS
  `font-weight`/`font-stretch` still work.
- **Palette-agnostic:** this lens is *not* tied to heyhoncho's blush-red, or to
  dark. Commit to *a* hue; the signature moves (preloader **invert**, curtain wipe,
  masthead grammar) work on any base — invert = ink-screen on a light canvas just
  as well as light-on-dark.

> **Note on dashes & View Transitions:** the masthead em-dash separators ("01 —
> Spatial Identity") are intentional editorial typography — react-doctor flags them
> as AI-slop; keep them. And React `<ViewTransition>` is still missing from stable
> `@types/react` in Next 16; keep the typed re-export shim (`lib/view-transition.tsx`,
> `React as any`) — see the `awesome-react-animations` view-transitions reference.

## Do / Don't

**Do:** commit to one loud concept; go enormous on the display type; leave huge
empty space; number and caption like a magazine; choreograph a few motion moments
with one signature easing; use real characterful typefaces.

**Don't:** fill every region; use a default font at one weight; center-and-evenly-
pad everything; add the same fade-up to every block; chase effects with no concept.
Editorial is *loud but disciplined* — restraint is what keeps "loud" from "messy".

## References

- `references/case-study-heyhoncho.md` — full teardown: tokens, type pair, stack,
  composition, and the live motion choreography, with `heyhoncho-index.png` and
  `heyhoncho-detail.png`.
- `references/organic-motion-recipe.md` — the seven ingredients of organic motion
  and a reference-stack → modern-React translation.
- `references/more-references.md` — three more editorial teardowns (Gretel,
  COLLINS, BUCK) with measured tokens + screenshots, mapping the style's
  bold → serif-elegant → clean-grid spectrum.
- Pair with **awesome-frontend-design** (method) and **awesome-react-animations**
  (smooth 60fps implementation, View Transitions, split-text staggers, Lenis).
