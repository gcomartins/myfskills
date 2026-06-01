---
name: luxury-web-design
description: >
  The LUXURY / refined / "quiet luxury" design lens — restrained, elegant,
  high-end interfaces that signal quality through calm, space, and craft rather
  than loudness. Use when the user wants this look or names it: "luxury",
  "premium", "high-end", "elegant", "refined", "sophisticated", "quiet luxury",
  "boutique", "editorial-elegant", "apothecary/atelier", or is designing for
  beauty, fashion, fine food/wine, hospitality, jewelry, architecture, or a premium
  brand. This is a STYLE LENS — apply on top of the awesome-frontend-design method.
  Anchored in a teardown of aesop.com. Core feel: a warm restrained palette, a
  refined typeface (high-contrast serif OR a quiet precise grotesque), small
  understated type, vast calm space, slow elegant pacing, and subtle motion.
---

# Luxury / Refined — the lens

Luxury on the web is **restraint, not ornament**. It signals quality the way a
boutique does — through calm, space, materials, and precision, never through more.
"Quiet luxury" especially: nothing shouts; the confidence is in how *little* is
done and how perfectly it's done. The mistake is equating luxury with gold
gradients and big serifs; real high-end is usually understated.

Apply on top of the `awesome-frontend-design` method. Worked example with tokens:
`references/case-study-aesop.md` (+ `aesop.png`).

## What makes it "luxury"

- **A warm, restrained palette.** Not stark white — a **warm neutral canvas**
  (cream/ivory/bone, e.g. Aesop's `#FFFEF2`), soft near-black text (`#333`, not
  pure `#000`), and **one muted, sophisticated accent** (terracotta, olive, oxblood,
  ink — Aesop's brick `#CA432F`). Desaturated and tasteful, never neon.
- **Refined type, set small.** Either a **high-contrast serif** (literary,
  elegant) or a **quiet precise grotesque** (Aesop uses Suisse Int'l) — the key is
  *refinement*, not size. Headings are often **modest** (Aesop H1 ~36px), letting
  generous space and copy carry the page. Impeccable micro-typography: generous
  leading, considered tracking, real punctuation.
- **Vast calm space + slow rhythm.** Lots of margin; few elements per view;
  unhurried vertical pacing. Space communicates value and confidence.
- **Materials and craft over graphics.** Beautiful, calm full-bleed photography
  (product, texture, place), often muted/natural-light. Thin hairline rules,
  hairline-outline buttons, `border-radius: 0` or a tiny consistent radius.
- **Understatement in UI.** Small caps or refined labels; quiet nav; restrained
  buttons (thin outline or plain text + arrow). Nothing chunky or loud.
- **Precision is the luxury.** Perfect alignment, consistent spacing, considered
  details. The "expensive" feeling comes from control, not decoration.

## Composition recipe
- Warm neutral canvas + soft near-black text + one muted accent. Radius 0 or tiny.
- Refined type (high-contrast serif or quiet grotesque) at **modest** sizes; big
  leading; lots of margin. Body copy is set beautifully and given room.
- Calm full-bleed imagery; hairline rules; thin-outline or text+arrow buttons.
- Fewer elements, more space, slower vertical rhythm. Center or gently asymmetric.

### Layout archetypes for a multi-section site
"Spacious, one-at-a-time" can read as *empty* if you only add margin. Use real
structure so calm feels composed, not bare:
- **Alternating stacked index** (for a work/project list): one project per row,
  large image alternating left/right of a small refined caption block, generous
  vertical gaps. Reveals one row at a time on scroll — never a dense grid.
- **Hairline-divided index:** thin top rules separating rows with a label/value
  masthead; structure carried by alignment + hairlines, not boxes.
- **Centered single-focus sections:** one statement or one plate centered in a
  wide calm field; the surrounding space *is* the composition.
Anchor text to a consistent measure (not full-bleed paragraphs); let the canvas,
not borders, separate sections.

## Motion direction
**Slow, soft, and sparse.** Long, gentle fades and eases (slightly longer
durations, smooth/quiet easing); subtle image reveals and cross-fades; refined
hover states (a quiet underline, a soft fade). No bounce, no snap, no busy
choreography. The pacing itself reads as elegant. (Implementation: awesome-react-
animations; honor reduced motion.)

## Do / Don't
**Do:** use a warm neutral palette + one muted accent; keep type refined and
*small*; leave vast calm space; let beautiful imagery and perfect alignment carry
it; move slowly and subtly.
**Don't:** equate luxury with big gold serifs, gradients, glow, or ornament; use
pure stark white/black; crowd the layout; add bouncy or snappy motion; shout.
Luxury is what you *leave out* — restraint and precision are the whole game.

## References
- `references/case-study-aesop.md` — tokens + teardown, with `aesop.png`.
- `references/more-references.md` — three more teardowns (The Row, Le Labo,
  Hermès) covering stark vs. warm-atmospheric luxury, with tokens + screenshots.
- Pair with **awesome-frontend-design** (method) and **awesome-react-animations**
  (slow, subtle motion; reduced-motion).
