# Composition, Type & Color — the static craft

How to make a page look *art-directed* before any motion. Grounded in the
heyhoncho teardown (`case-study-heyhoncho.md`) but generalized.

## Type system — the highest-leverage decision

**Pair two faces in tension.** One does almost nothing alone; the contrast is the
identity.
- **Display face with attitude** — condensed/heavy/quirky grotesque or a
  characterful serif. Set it HUGE for titles and one-line statements. (Honcho: FK
  Screamer at ~250px.) This is the page's "voice."
- **Refined counterpoint** — a high-contrast serif (often italic) or a clean
  grotesque, for labels, captions, body, credits. (Honcho: Heldane Display italic.)

**Use the pair semantically**, not randomly:
- label/value masthead: *serif-italic descriptor* + GROTESQUE CAPS VALUE
  ("*Client:* Grubhub", "*Photographer* IAN LORING SHIVER").
- big statement in display; supporting prose in the serif/body face.

**Set a tight scale ramp** — most pages need only ~4 sizes: a massive display
step, a section step, body, and a micro label (often mono/uppercase, tracked
out). The *jump* between massive and micro is the drama; avoid a smooth gradient
of similar sizes.

**Micro-typography is the tell of craft:**
- tight negative tracking on huge display type; normal/loose on small caps labels;
- generous leading on serif body, tight leading on stacked display lines;
- real quotes, proper case, optical alignment;
- uppercase + letter-spacing for tiny mono labels/eyebrows.

**Where to find characterful faces** (vs. Inter/Geist default): foundries like
Klim, Pangram Pangram, Florian Karsten, Dinamo, ABC; or well-chosen Google fonts
(e.g. a condensed display + a contrast serif). The point is *commit to a face with
a personality*, not the safe default.

## Color — pick a hue and live in it

- **Few colors, hard.** Many of the best sites are essentially **monochromatic**:
  one hue at three values — a tinted **canvas**, a **saturated** mid for accents,
  a **dark** version for text. (Honcho: blush `#FFEEEE` / red `#FC221C` / deep red
  `#A10A06`.)
- **Skip gray defaults.** Tint your neutrals toward the hue (a warm off-white, a
  near-black with a hint of the brand color) instead of pure gray/white/black.
- **One accent, used decisively** — for the few things that must pop. Two accents
  usually means no point of view.
- Decide a **stance on contrast**: blush-on-red is low-contrast and moody;
  black-on-white is high-contrast and stark. Either works if it's *chosen*.

## Composition & layout

- **Negative space is the luxury signal.** Give hero type and key images room to
  breathe; emptiness reads as confidence and curation. Resist filling every region.
- **Asymmetry + edge-anchoring.** Anchor a headline to the bottom-left, float an
  image off-center, push a label to a margin. Centered-everything is safe and
  forgettable; a deliberate off-balance composition has tension.
- **Scale contrast as drama.** One enormous element + small precise satellites
  (a 250px title beside 12px mono labels). The contrast carries the page.
- **Editorial structures.** Number items (01, 02, 03…); pair each with a
  label/value caption; treat an index like a magazine contents page; let one
  column be sticky while another scrolls.
- **A grid you then break.** Establish columns for order, then deliberately break
  out of them for emphasis — a full-bleed image, a title that overhangs the margin.
- **Commit to corner geometry.** Honcho uses `border-radius: 0` everywhere → hard,
  editorial. Or pick one consistent radius. The mistake is mixing radii ad hoc.
- **One base spacing unit** (e.g. 4px) so rhythm is consistent; vary by multiples,
  not arbitrary values.

## Imagery & detail

- **Treat images as plates.** Consistent crop/aspect, generous margins, sometimes
  a subtle scale-on-hover or clip-reveal. Duotone or a color wash can fold images
  into the palette.
- **Hover is an opportunity.** Image clip/curtain reveals, link underdraws,
  magnetic buttons, a custom cursor — small, consistent interactions that signal
  care. (Honcho stacks two images per link for a hover clip-reveal.)
- **Consistency over novelty.** The same easing, the same caption style, the same
  spacing unit repeated everywhere is what makes it feel *designed* rather than
  decorated.

## The quick gut-check before shipping
1. Can I name the concept in 3 words? Does every screen obey it?
2. Two typefaces in tension, used semantically? Real scale contrast?
3. One hue, ~3 values? Neutrals tinted, not gray?
4. Is there enough empty space to feel intentional?
5. Is anything anchored/asymmetric, or is it all centered stacks?
6. Consistent corners, spacing unit, and one easing throughout?
If any answer is "no," that's the highest-leverage fix.
