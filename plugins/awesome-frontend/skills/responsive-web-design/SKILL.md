---
name: responsive-web-design
description: >
  Make any site look intentional at every width — phone, tablet, desktop — and
  never break or scroll sideways on mobile. Use whenever responsiveness is in
  play: "make it work on mobile", "it looks broken on my phone", "responsive",
  "mobile layout", "fluid type", "it overflows / scrolls sideways", "tablet
  breakpoint", or any time you build a layout that must survive small screens.
  This is a lens-AGNOSTIC implementation skill (like awesome-react-animations and
  awesome-webgl) — apply it on top of the awesome-frontend-design method and ANY
  style lens. Core belief: design fluid by default, verify at real widths, and
  let nothing ever cause horizontal scroll.
---

# Responsive Web Design — fluid by default, verified at real widths

A layout isn't "done" until it's beautiful on a 390px phone. The trap is
designing at desktop and bolting on breakpoints — you get overflow, cramped
type, and decorative chrome colliding with content. Design mobile-first and
fluid, and the big screens come for free.

## The non-negotiables

1. **Nothing causes horizontal scroll. Ever.** A sideways-scrolling page reads as
   broken. The detector is one line: at a mobile width, `document.documentElement.scrollWidth` must equal `window.innerWidth`. Common culprits: raw `vw`
   type, fixed `w-[NNrem]` blocks, negative-positioned / oversized decorative
   elements, and unclamped marquees. Belt-and-suspenders: put `overflow-x: clip`
   on a top-level wrapper (it doesn't create a scroll container, and `position:
   fixed` children escape it). But find and fix the real cause too.

2. **Fluid type with `clamp()`, not raw `vw` — especially for multi-glyph or
   flex'd text.** `text-[24vw]` on a 5-letter word in a no-wrap flex is `120vw`
   → it overflows the screen. Use `clamp(min, preferredVw, max)` so type scales
   smoothly *and* is bounded at both ends. If you must use raw `vw`, do the math:
   `glyphs × vw` has to stay under ~90.

3. **Fixed chrome collides with whatever sits at the top edge.** A fixed
   wordmark/nav floats over scrolling content — fine for sparse content, but a
   section's own top row (a masthead, an index, a label bar) will *overlap* it,
   and on narrow screens everything is full-width so the overlap is unreadable.
   Give sections enough **mobile top padding** to clear the chrome (revert at
   `sm:`), or hide redundant decorative rows on mobile. Desktop usually hides the
   collision because the items spread across the width — so this bug is
   mobile-only and easy to miss on a desktop screenshot.

4. **Stack, don't shrink.** Multi-column grids/tables/indexes collapse to 1–2
   columns on mobile (`grid-cols-1 sm:grid-cols-2 lg:grid-cols-4`), side-by-side
   hero columns stack (`flex-col lg:flex-row` / `grid-cols-1 lg:grid-cols-2`).
   Don't just scale a desktop grid down — it gets unreadable.

5. **Scale spacing too.** Desktop `py-32`/`gap-16` is suffocating on a phone.
   Step rhythm down on mobile (`py-20 sm:py-32`). Generous-but-proportional.

6. **Touch + reduced-motion.** Disable custom cursors on coarse pointers
   (`pointer: coarse`). Anything that only reveals on `:hover` needs a
   non-hover fallback on touch. Tap targets ≥ ~44px. (Reduced-motion gating:
   see awesome-react-animations.)

7. **Media never overflows.** `max-w-full`, intrinsic `aspect-[..]`; avoid fixed
   pixel heights that don't fit a short viewport.

## Verify at real widths — don't assume

- **Use a viewport you can actually control.** A device preset (e.g. Claude
  Preview's `mobile` 375×812, or DevTools device mode) reflows the page. Note:
  merely resizing a browser *window* often does NOT change the captured
  viewport — the screenshot stays desktop-width and you "verify" nothing.
- **Screenshot each section** at 375–390px (and 768 for tablet). Look for: side
  scroll, type bigger than its box, chrome/masthead overlap, single-column
  cramping.
- **Programmatic overflow check** per section:
  `el.scrollIntoView(); doc.scrollWidth > innerWidth` → must be false. (With a
  smooth-scroll lib like Lenis, prefer `window.scrollTo({top: el.offsetTop+N, behavior:'instant'})` — native `scrollIntoView` can fight the smooth-scroll
  rAF and land mid-transition.)
- A build that compiles, lints, and has the right SSR markup can still render
  unstyled or overflowing. **Pixels are the source of truth.**

## Breakpoints (Tailwind defaults)

`sm 640 · md 768 · lg 1024 · xl 1280`. Mobile-first: unprefixed = the phone
baseline; add `sm:`/`lg:` to enhance up. Reach for fluid `clamp()` to need fewer
breakpoints at all.

## References / pairing

- **awesome-frontend-design** — the method (this skill is the responsive layer
  under it).
- **Style lenses** (editorial/swiss/brutalist/luxury/portfolio) — each has mobile
  caveats: editorial's giant `vw` type needs `clamp`; swiss's 12-col grid
  collapses to fewer; brutalist's offset shadows/borders stay but type scales.
- **awesome-react-animations** — reduced-motion, touch, and not animating layout
  properties.

## One line

Mobile-first, fluid `clamp()` type, zero horizontal scroll, sections that clear
the fixed chrome — then verify on a real 390px viewport, because pixels don't lie
and a passing build doesn't prove the layout holds.
