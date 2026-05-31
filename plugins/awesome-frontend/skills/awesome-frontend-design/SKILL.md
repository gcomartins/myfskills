---
name: awesome-frontend-design
description: >
  The method for designing distinctive, high-craft frontends — the style-agnostic
  core: how to make ANY site or page feel art-directed and intentional rather than
  templated. Use whenever look-and-feel matters and the user hasn't named a
  specific aesthetic: landing pages, portfolios, marketing sites, hero sections,
  case studies. Trigger it on "make it look designed / premium / intentional / not
  generic", "this looks AI-made", "give it a real identity", or any composition,
  type-pairing, color, spacing, or motion-direction question. This is the DESIGN
  METHOD; for a specific look, pair it with a style LENS skill (editorial-web-design,
  swiss-minimal-design, brutalist-web-design, luxury-web-design). For making the
  motion it calls for run at 60fps, pair with awesome-react-animations. Core
  belief: great design is a few bold, committed decisions executed with discipline.
---

# Awesome Frontend Design — the method (core)

Most frontends look generic because they make no decisions: a default font, a
neutral palette, even padding everywhere, motion bolted on last. Distinctive work
**commits to one strong idea** and executes it with restraint. This skill is the
*style-agnostic method* for doing that — the part that's true whether the result
is editorial, Swiss, brutalist, or luxury. For a specific aesthetic, layer a
**lens** on top (see "Pick a lens" below); the lens supplies the vocabulary and a
real reference teardown, this core supplies the discipline.

## The method — five decisions, in order

### 1. Concept first — one bold idea
Decide the *attitude* before any pixels and name it in ~3 words. Then let that
concept **veto** choices. A page trying to be elegant AND playful AND minimal AND
techy reads as none of them. The concept is the spine every later decision serves.

### 2. Type as the loudest voice
Type carries more identity than anything else. The strongest systems **pair two
faces in tension** and use them **semantically** (one for statements, one for
labels/credits/body). Set a **tight scale ramp** — usually ~4 sizes: one massive
display step, a section step, body, and a micro label. The *jump* between massive
and micro is the drama. Sweat micro-typography (tracking, leading, real quotes,
optical alignment). Details + how to choose faces: `references/composition-and-type.md`.

### 3. Color — commit to a point of view
Few colors, used hard. Decide a palette with a stance (a committed hue, a stark
mono, a restrained neutral system) instead of the generic gray-on-white + one blue
accent. Tint neutrals toward the concept; pick contrast deliberately.
`references/composition-and-type.md` covers building the palette.

### 4. Composition — structure with intent
Establish a grid, then break it on purpose. Use **negative space as a luxury
signal**, **asymmetry and edge-anchoring** over centered-everything, and **scale
contrast** as drama. Commit to one corner geometry and one base spacing unit.
Full layout playbook: `references/composition-and-type.md`.

### 5. Motion direction — choreograph, don't sprinkle
Decide what moves, in what order, with what feeling — a scripted timeline (load →
reveal → scroll beats → transitions), not fade-up-on-everything. The biggest tell
of intentional motion is **custom easing** (expo/power/spring, never the browser
default) reused consistently. Principles: `references/motion-direction.md`. Then
implement it smoothly per the **awesome-react-animations** skill (this core decides
*what* and *why*; that skill keeps it 60fps).

## Process

1. Write the concept (3 words) and the reference feeling.
2. Build the type system (display/label pair, scale ramp, case, tracking).
3. Pick the palette (stance + values) and fix radius + base spacing unit.
4. Compose on a grid you then break (anchor, float, negative space, scale contrast).
5. Choreograph motion (sequence + one signature easing), then implement smoothly.
6. Sweat details (hover reveals, cursor, consistent corners, optical alignment).

## Pick a lens (style packs)

When the user wants a *specific* aesthetic — or you've chosen one for them — apply
the matching lens skill on top of this method. Each lens carries that style's
vocabulary, do/don't rules, and a real reference teardown:

- **editorial-web-design** — loud magazine/lookbook/agency: type in tension,
  numbered indices, masthead label/value, huge negative space, choreographed
  "organic" motion. (Reference: heyhoncho.)
- **swiss-minimal-design** — objective grid systems, neutral precise type, lots
  of white, hierarchy by scale + spacing.
- **brutalist-web-design** — raw, high-contrast, hard edges, system/mono type,
  deliberate "ugly-beautiful".
- **luxury-web-design** — high-contrast serifs, restrained palette, slow elegant
  pacing, subtle micro-interactions.

If no lens fits, you can still produce strong work from this method alone — just
make the five decisions deliberately and commit.

## Anti-patterns (the "generic AI site" smell)

- Inter/Geist everywhere at one weight; no display face, no contrast.
- Gray text on white, one blue/indigo accent, `rounded-xl` on everything.
- Perfectly centered, evenly-padded stacks; no negative space, no anchoring.
- Motion = the same fade-up with the same duration and default `ease` on every block.
- Effects with no concept behind them. More is not direction; commitment is.

## One line

Design is subtraction and commitment: one concept, two typefaces in tension, a
committed palette, lots of space, and choreographed motion — then made smooth.
The lenses tell you *which* commitments; this core makes sure you actually commit.
