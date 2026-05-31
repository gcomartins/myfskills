---
name: brutalist-web-design
description: >
  The BRUTALIST / neo-brutalist web design lens — raw, high-contrast, hard-edged
  interfaces that show their structure and feel bold and unpolished on purpose. Use
  when the user wants this look or names it: "brutalist", "neo-brutalist",
  "neubrutalism", "raw", "bold/flat", "hard borders", "anti-design", "indie/
  startup edgy", "memphis-ish", or wants something loud, high-energy, and
  un-corporate (indie products, dev tools, creative tools, drops, communities).
  This is a STYLE LENS — apply on top of the awesome-frontend-design method.
  Anchored in a teardown of gumroad.com. Core feel: thick black borders, flat
  offset shadows, bright flat color blocks, big bold type, hard edges, visible
  structure, zero gloss.
---

# Brutalist / Neo-brutalist — the lens

Brutalism on the web borrows from architectural brutalism: show the structure,
refuse polish, be honest and blunt. The modern, friendlier strain — **neo-
brutalism / neubrutalism** — keeps the rawness (hard borders, flat shadows, loud
flat color, system type) but makes it usable and playful. It reads as bold,
energetic, and un-corporate — the deliberate opposite of soft gradient SaaS.

Apply on top of the `awesome-frontend-design` method. Worked example with tokens:
`references/case-study-gumroad.md` (+ `gumroad.png`).

## What makes it "brutalist"

- **Hard borders, everywhere.** Thick (2–3px) **solid black** outlines on cards,
  buttons, inputs, sections. The border is the primary styling device.
- **Flat offset shadows, not blur.** A solid `box-shadow: 6px 6px 0 #000` (no
  blur, no spread) gives the chunky "sticker" depth. On hover, the element often
  shifts and the shadow collapses (translate + reduce offset).
- **Bright, flat color blocks.** Saturated, un-tinted fills (hot pink, electric
  purple, lime, primary yellow) on an off-white/paper canvas. No gradients, no
  soft tints. Color is used in big confident areas.
- **Big, blunt type.** A bold grotesque (or system/`Arial`/mono) set large and
  tight. Often a slightly characterful display face for headings (Gumroad uses ABC
  Favorit) over a plain body (Arial). Type is loud and unfussy.
- **Hard edges or minimal radius.** Either `radius: 0` (pure brutalist) or a small
  consistent radius (~4–8px) for the friendlier neo-brutalist look — but the black
  border + flat shadow matter far more than the corner.
- **Visible structure, "honest" UI.** Boxes look like boxes; buttons look pressable
  and chunky; the layout grid is obvious. Embrace a little asymmetry and density.
- **Optional rawness.** Some brutalist sites go further: default-blue links,
  monospace, exposed system fonts, intentionally "unstyled" bluntness. Pick how raw
  vs. how playful for the concept.

## Composition recipe
- Off-white/paper canvas. One or two **loud flat accent colors**.
- Black 2–3px borders + flat offset shadows on every interactive surface.
- Bold grotesque, large, tight; plain body. Decide radius (0 = raw, ~6px = playful).
- Chunky buttons (border + flat shadow + active "press" that shifts + collapses
  shadow). Hover = translate + shadow change, snappy.
- Confident color blocks; obvious grid; a bit of density and asymmetry is good.

## Motion direction
**Snappy and mechanical**, not smooth-organic. Short, abrupt transitions; the
signature move is the **press**: on hover/active, translate the element by the
shadow offset and shrink the shadow so it looks physically pressed. Little to no
easing (or a fast ease) suits the blunt feel. Keep it cheap (transform/opacity)
and reduced-motion-aware. (Implementation: awesome-react-animations.)

## Do / Don't
**Do:** commit to thick black borders + flat offset shadows; use loud flat colors
in big areas; set type large/bold/tight; make buttons chunky and "pressable"; keep
edges hard.
**Don't:** add blur shadows, gradients, or glassy effects; use timid pastel-on-
white; over-round corners into friendliness; center-and-pad everything evenly.
Brutalism that's been "cleaned up" with soft shadows is just generic SaaS again —
the harshness is the identity.

## References
- `references/case-study-gumroad.md` — tokens + teardown, with `gumroad.png`.
- Pair with **awesome-frontend-design** (method) and **awesome-react-animations**
  (snappy press interactions, reduced-motion).
