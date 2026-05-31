# Case Study — swissted.com (Swiss / International Typographic Style)

Mike Joyce's project redrawing rock-show flyers as Swiss-style posters — and the
site itself is textbook International Typographic Style. Screenshot: `swissted.png`.

## Tokens (measured)
- **Type:** **Helvetica** for everything (heading + body). No second face. The
  identity is Helvetica at different sizes/weights, nothing else.
- **Color:** black `#000000` text on white `#FFFFFF`; accents are flat **primary
  colors** — red `#FF0000`, blue, yellow `#FFFF99`, green — used in the poster art,
  not as UI chrome.
- **Geometry:** `border-radius: 0`. Thin black horizontal rules divide sections.
- **Scale:** big wordmark ("swissted" set huge, lowercase Helvetica), H1 ~72px,
  plain text nav.

## Composition
- **Giant lowercase Helvetica wordmark** spanning the top, then a **thin black
  rule**, then a plain text nav ("Home | About | Shipping") with a search box —
  utilitarian, unstyled, perfectly aligned.
- **A strict grid of poster tiles** below — equal cells, consistent gutters, each
  poster a self-contained Swiss composition (geometric shapes, primary colors,
  Helvetica, diagonal/circular motifs, objective abstraction).
- Everything sits on the grid; the rhythm and alignment *are* the design. No
  shadows, no gradients, no rounded corners, no decoration anywhere.

## Why it works (the transferable lesson)
It commits totally to the system: **one typeface, a visible grid, black/white +
primary accents, zero ornament.** Hierarchy comes purely from size, weight, and
position. Nothing is added "to make it nicer" — and that discipline is exactly
what reads as rigorous and timeless. The web translation: a real CSS grid you
respect everywhere, a single grotesque at a tight scale ramp, one accent color,
`radius: 0`, mathematical spacing, and the confidence to leave big empty fields.

## Other reference points (same school)
Müller-Brockmann grid systems, Unimark/Vignelli (NYC subway), Massimo Vignelli's
"a few good typefaces" doctrine, Dieter Rams' "less but better." When unsure, ask:
"could I remove this and lose nothing but decoration?" If yes, remove it.
