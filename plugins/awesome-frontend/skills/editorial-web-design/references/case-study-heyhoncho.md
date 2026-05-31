# Case Study — heyhoncho.com (deconstruction)

A teardown of HONCHO!'s portfolio (a NYC/LA production studio) — a reference-grade
example of loud-editorial web design with organic motion. Captured by inspecting
the live site: rendered HTML, design tokens, and screenshots of the projects
index and a project detail page. Use it as a concrete model for the principles in
SKILL.md.

Reference screenshots bundled alongside this file:
`heyhoncho-index.png` (the projects index hero) and `heyhoncho-detail.png` (a
project "title-card" hero). Read them to see the composition described below.

## The concept (in 3 words)
**Loud editorial fashion-print.** Brash, confident, magazine-grade. Every choice
below serves that one attitude.

## Design tokens (measured, not guessed)
- **Palette — monochromatic red:**
  - canvas / background: `#FFEEEE` (blush pink)
  - primary / accent / buttons: `#FC221C` (vivid red)
  - text / links: `#A10A06` (deep maroon-red)
  - No grays. No second hue. Three values of one color.
- **Type pair (tension):**
  - **FK Screamer Legacy** (Florian Karsten) — heavy, condensed, shouting
    grotesque. The loud voice: titles, statements, nav, client names. H1 ≈ 250px.
  - **Heldane Display** (Klim) — high-contrast serif, frequently *italic*. The
    refined voice: labels, credits, captions.
- **Geometry:** `border-radius: 0` on everything (inputs, buttons, images) →
  sharp, brutalist-editorial. Base spacing unit 4px.

## Composition
- **Projects index:** an editorial contents spread. A blush field with one
  portrait image floated upper-right and a huge red caps headline anchored
  bottom-left ("MADE FOR ALL. / MADE FOR NEW YORK." + client "UNIQLO"), plus a
  running index number (01, 02, 03 …). As you scroll, the caption swaps per
  project — a sticky/updating label column synced to the column of images.
- **Project detail hero:** a centered "title card" — the project name set
  enormous in red grotesque (e.g. "EAT THE FEES"), with serif-italic credit lines
  beneath ("*Client:* Grubhub", "*Photographer* IAN LORING SHIVER"). Acres of
  negative space; the title is isolated like a movie title card.
- **Recurring system — label/value masthead:** serif italic descriptor + caps
  grotesque value. Used for every credit. It reads like a magazine masthead or
  film credits and ties the whole site together.
- **Negative space as luxury.** Both views are mostly empty canvas. The emptiness
  is the styling.

## The tech stack (how the motion is built)
Identified from the live `<script>` tags and data attributes:
- **Webflow** (`generator: Webflow`, website-files.com CDN) — the base build.
- **GSAP 3.14 + ScrollTrigger** — the animation engine (scroll-synced reveals,
  parallax, scrubbing). 95 elements carry `will-change` → transform/opacity,
  GPU-composited.
- **Splitting.js** — text split into chars (40 `data-splitting="chars"`). Targets
  include `loading-text-one/two` (preloader) and `gallery-title-text` /
  `gallery-client-name-text` (per-project captions) → kinetic char-stagger reveals.
- **Barba.js 2.10** (`data-barba`, `data-barba-namespace`) — SPA-style page
  transitions between index and project pages (no hard reload → continuous feel).
- **Locomotive Scroll** (`data-scroll-container`) — smooth/inertial scrolling
  (the "organic" weight).
- **PreloadJS** + a `progress`/`percent` counter and `loader` text — a full intro
  preloader that counts assets to 100% before the reveal.
- **video.js** — custom players for project reels; jQuery (Webflow dep).

## The motion choreography (what actually happens)
1. **Intro preloader:** a 0→100 counter while PreloadJS fetches imagery; two lines
   of `loading-text` animate in char-by-char (Splitting + GSAP). The load screen
   *is* the first impression, and it buys time so the reveal is instant.
2. **Reveal:** preloader curtains away; hero type staggers in.
3. **Scroll beats:** Locomotive drives smooth scroll; ScrollTrigger reveals each
   project's image and char-staggers its title + client caption as it enters;
   subtle parallax between caption and image.
4. **Hover:** each project link contains two stacked identical images — a clip /
   curtain reveal on hover (one image wipes/scales over the other).
5. **Navigate:** Barba transitions the index → the project's centered title card
   without a reload; the title-card hero then plays its own reveal.
6. **Easing:** expo/power curves throughout — nothing uses the browser default
   `ease`. This is the biggest reason it feels "organic".

## What to steal (translated to a modern React/Next stack)
You don't need Webflow/Barba/Locomotive — map each idea to current tools:
- Smooth scroll → **Lenis** (lerp ~0.12, native touch). Gate on reduced-motion.
- Kinetic type → split text yourself (or a tiny helper) + framer-motion variants
  with `staggerChildren`; mask each line with `overflow:hidden`.
- Scroll reveals/parallax → framer-motion `whileInView` + `useScroll`/`useTransform`.
- Page transitions → **React View Transitions** (or framer-motion layout/AnimatePresence).
- Preloader → a real asset-preload gate + a counted 0→100 + char-stagger reveal.
- Hover clip-reveal → two layers + `clip-path`/`scale` on hover (transform/opacity).
- Custom easing → reuse one expo curve everywhere, e.g. `[0.16, 1, 0.3, 1]`.
Implement all of it per the **awesome-react-animations** skill so it stays 60fps.

## Live capture — confirmed by driving the site (not just the code)
Watching it run added detail the static analysis couldn't:
- **Preloader inverts the palette.** The intro is a **full-bleed red** screen with
  **white** type — the studio statement "A FULL SERVICE PRODUCTION COMPANY / BASED
  *in* NYC AND LA" (note "*in*" set in Heldane italic inside the Screamer caps —
  faces mixed *within one line* for emphasis). It then flips to the blush/red site.
- **Curtain reveal with an oversized wordmark.** Between preloader and content, a
  giant "HEY!" sweeps the screen as the red curtain wipes up to reveal the blush
  gallery. The greeting itself is the transition.
- **The project "plates" are autoplaying videos, not stills** — silent reels
  (a ballet dancer on a theatre stage, a player in a gym) playing in the column.
- **Only the variable caption line re-animates.** As each project crosses center,
  the sticky left caption keeps "MADE FOR ALL." and the client ("UNIQLO") but the
  **middle city line char-shuffles out and the next assembles in** (New York →
  Boston → Chicago). The index number (01→02→03, set in Heldane serif) updates in
  sync. Mid-transition you can see letters at mixed opacity/offset — a char crossfade.
- **Barba transition is a shared-element morph.** Clicking a plate sweeps a **red
  curtain up from the bottom**, swaps the URL with **no reload**, and the caption
  you were reading becomes the **centered title-card** on the detail page —
  "MADE FOR ALL. MADE FOR CHICAGO." huge in red, with "*Client:* Uniqlo" and
  "*Photographer* JOE PERRI" in serif-italic-label + caps-value beneath. The text
  persists across the navigation; it doesn't hard-cut.
- Fixed nav top-right and the "HONCHO!" logo top-left stay put through all of it;
  a LiveChat widget sits bottom-right.

## The transferable lesson
None of this is "more effects." It's **one concept** (loud editorial), **one hue**
(red on blush), **two typefaces in tension** (Screamer + Heldane), **huge negative
space**, and **choreographed motion with custom easing**. Discipline and
commitment — not volume — make it look authored.
