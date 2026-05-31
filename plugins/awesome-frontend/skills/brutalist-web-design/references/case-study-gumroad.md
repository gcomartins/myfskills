# Case Study — gumroad.com (Neo-brutalist)

Gumroad is the canonical mainstream neo-brutalist product site. Screenshot:
`gumroad.png`.

## Tokens (measured)
- **Type:** **ABC Favorit** (a characterful neo-grotesque) for headings, **Arial**
  for body. Big and tight — H1 ~96px ("Go from 0 to $1").
- **Color:** paper/off-white canvas `#F4F4F0`; black `#000` text; loud flat
  accents — **hot pink `#FF90E8`** and **electric purple `#625BF6`**; a blue link.
  All flat, saturated, no gradients/tints.
- **Geometry:** small radius (~6px) but the identity is the **2–3px solid black
  borders** + **flat (blur-less) offset shadows** on cards and the chunky black
  "Start selling" button. Top-right nav item is a pink-filled tab.

## Composition
- Black top bar with plain nav; a pink "Start selling" cell breaking the bar.
- Centered huge headline + short blurb + a **chunky black button** and a bordered
  search field with a hard outline.
- Below: **outlined cards** with thick black borders containing big blunt headings
  ("Sell anything", "Make your own road") and a playful illustration in a bordered
  "browser" frame. Everything looks like a box with an honest edge.

## The signature interactions (neo-brutalist)
- **Flat offset shadow** `box-shadow: Xpx Ypx 0 #000` (no blur) = sticker depth.
- **Press on hover/active:** translate the element by the shadow offset and shrink
  the shadow, so the button looks physically pressed into the page. Snappy, little
  easing.
- Hard black borders define every interactive surface; color fills are big and flat.

## Why it works (the transferable lesson)
It's confident and un-corporate: **hard borders + flat shadows + loud flat color +
big blunt type**, with zero gloss. The friendliness comes from playful color and
illustration, not from soft shadows or gradients — so it stays bold. The web
translation: black borders and flat offset shadows as your core component styling,
two loud accent colors used in big areas, a characterful bold heading face over a
plain body, and chunky "pressable" buttons. Decide how raw (radius 0, system/mono,
default-blue links) vs. how playful (small radius, bright palette, illustration)
to push it.

## Other reference points (same family)
Pure brutalism: brutalistwebsites.com gallery, early-web exposed-HTML aesthetics,
default browser styles as a statement. Neo-brutalism: Figma's bolder marketing
moments, indie dev-tool landing pages, "neubrutalism" UI kits. The throughline is
*honesty + hardness* over polish.
