# Motion Direction (style-agnostic)

How to *direct* motion so a site feels authored, regardless of aesthetic. This is
about the **decisions** — what moves, when, and how it feels. For implementing any
of it at 60fps in React, follow the **awesome-react-animations** skill. For a
style's specific motion vocabulary, see its lens (e.g. the "organic" recipe lives
in editorial-web-design).

## Principles

1. **Choreograph a timeline, don't sprinkle.** Script the sequence — load → reveal
   → scroll beats → transitions — and decide overlap and rhythm. The difference
   between "nice" and "authored" is *intentional overlap and delay*, not the same
   fade-up on every block.

2. **One signature easing.** Pick a curve and reuse it everywhere. Custom easing
   (expo/power/spring) instead of the browser default `ease` is the single biggest
   tell of intentional motion. A common cinematic curve: `cubic-bezier(0.16,1,0.3,1)`
   (expo-out); springs for interactive/gesture motion.

3. **Match motion to the concept.** Loud/editorial → big kinetic type, curtain
   transitions, inertial scroll. Minimal/Swiss → almost none; precise, brief,
   functional. Luxury → slow, soft, restrained. Brutalist → snappy, abrupt, or
   pointedly *no* easing. The motion *is* part of the style statement.

4. **Restraint scales with calm.** The more minimal the design, the less motion it
   wants. Choreograph a few moments well; leave the rest still. Busy ≠ designed.

5. **Reveal once; respect intent.** In-view reveals should fire once (not re-run on
   every scroll-by). Animate on `transform`/`opacity`. Always honor
   `prefers-reduced-motion` — gate large/continuous motion and smooth-scroll.

6. **Transitions create continuity.** Replace hard cuts with cover/curtain or
   shared-element transitions so navigation feels like one continuous space. In
   React this is View Transitions / `view-transition-name` (or AnimatePresence /
   layout).

## A simple direction checklist
- What's the *first* thing that moves, and does it earn attention?
- Is there one easing used everywhere, or a mess of defaults?
- Does the amount of motion match the concept's volume (loud vs. calm)?
- Do reveals fire once and stay on transform/opacity?
- Is there a reduced-motion path?
- Does navigating feel continuous (transitions) or stitched (hard cuts)?

Decide these, then hand implementation to **awesome-react-animations**.
