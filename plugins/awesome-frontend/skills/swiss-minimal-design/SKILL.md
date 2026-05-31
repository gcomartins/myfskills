---
name: swiss-minimal-design
description: >
  The SWISS / International Typographic Style / minimal design lens — objective,
  grid-driven, type-led interfaces with lots of white space and zero ornament. Use
  when the user wants this look or names it: "Swiss", "International Typographic
  Style", "grid system", "minimal", "clean", "Helvetica", "modernist", "Bauhaus-y",
  "objective", or is designing something that should feel rigorous, neutral, and
  premium-through-restraint (design studios, type foundries, editorial/portfolio,
  serious product marketing). This is a STYLE LENS — apply it on top of the
  awesome-frontend-design method. Anchored in a teardown of swissted.com. Core
  feel: a strict modular grid, a single neutral grotesque, flush-left text,
  mathematical spacing, primary-color accents, and hierarchy from scale/weight/
  position — never decoration.
---

# Swiss / Minimal — the lens

Swiss design (the International Typographic Style, Müller-Brockmann / Hofmann /
Bill) is *objective*: the grid and the type do the work, ornament is removed, and
beauty comes from proportion, alignment, and restraint. On the web it reads as
rigorous, calm, and quietly premium — the opposite of decorated. "Minimal" done
well is Swiss with the volume down further.

Apply on top of the `awesome-frontend-design` method. Worked example with tokens:
`references/case-study-swissted.md` (+ `swissted.png`).

## What makes it "Swiss"

- **A strict modular grid is the design.** Columns and baseline rhythm are
  visible in the alignment. Place everything on the grid; asymmetry *within* the
  grid creates interest (not random placement). Generous, mathematical margins.
- **One neutral grotesque, used at a few sizes.** Helvetica / Akzidenz-Grotesk /
  Neue Haas / Inter-as-a-stand-in. No display/serif pairing — the *type itself*
  is the identity, through size and weight, not character. Tight, deliberate
  hierarchy: a large heading, a mid, body, caption — set with real ratios.
- **Flush-left, ragged-right.** Left-aligned text blocks, consistent leading,
  generous line spacing. Avoid centered paragraphs and justified text.
- **Hierarchy from scale / weight / position — never ornament.** No shadows,
  gradients, borders-as-decoration, or rounded "friendly" corners. `border-radius:
  0`. If something needs emphasis, make it bigger, bolder, or move it on the grid.
- **White space is structural, not leftover.** Large empty fields are part of the
  composition; they set rhythm and importance.
- **Color: mostly black on white + one or two PRIMARY accents.** Swissted is
  literally Helvetica black on white with red/blue/yellow geometric accents. Use
  color sparingly and decisively (often a single red). Flat, saturated, no tints.
- **Geometry and objective imagery.** Circles, squares, diagonals, hard-edged
  shapes; photography (if any) is plain and documentary, full-bleed or grid-locked.

## Composition recipe
- Pick a column count (e.g. 12) and a baseline unit; show the grid in alignment.
- Set a type scale with clean ratios; left-align; one grotesque, 2–3 weights.
- Black/white base + one accent. Radius 0. One spacing unit, used mathematically.
- Compose asymmetrically *on the grid*; let big white space carry importance.
- Number/label things plainly; let the structure, not styling, communicate.

## Motion direction
Minimal and functional. Brief, precise transitions (short fades/slides on one
easing); no decorative or continuous motion. The restraint *is* the statement —
when in doubt, move less. (Implementation: awesome-react-animations; honor reduced
motion.)

## Do / Don't
**Do:** commit to one grotesque; align everything to a visible grid; use scale and
weight for hierarchy; leave large structural white space; one decisive accent;
hard edges.
**Don't:** add shadows/gradients/rounded corners "to soften it"; center everything;
pair decorative fonts; fill the space; use color decoratively. Softening Swiss
turns it into generic SaaS — the rigor is the point.

## References
- `references/case-study-swissted.md` — tokens + teardown, with `swissted.png`.
- Pair with **awesome-frontend-design** (method) and **awesome-react-animations**
  (minimal motion, reduced-motion).
