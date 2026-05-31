# framer-motion Patterns (the `m` component)

The patterns that cover most real React UI animation, written to stay off the
per-frame render path. Import the lightweight `m` component, not `motion`.

## Setup: LazyMotion + m (do this once)

```tsx
// MotionProvider.tsx ('use client')
import { LazyMotion, domMax } from 'framer-motion'
export default function MotionProvider({ children }) {
  // `strict` makes a stray `motion` import throw so you can't silently ship the
  // full bundle. `domMax` includes layout + drag; `domAnimation` is smaller but
  // excludes layout animations.
  return <LazyMotion features={domMax} strict>{children}</LazyMotion>
}
```

Wrap the app once (in `layout.tsx`/root), then use `<m.div>` everywhere. Hooks
(`useMotionValue`, `useSpring`, `useTransform`, `useScroll`, `useInView`,
`animate`, `AnimatePresence`) are unaffected — only the component changes.

> Renaming `motion`→`m` with a regex? Anchor on `motion.` and the bare
> identifier — a naive `s/motion/m/` corrupts `'framer-motion'` and any
> `motion-primitives` path.

## Motion values — the core of smooth (no re-render)

A motion value updates the DOM directly, off React's render cycle. This is how
you drive position/rotation/scale from input without `setState`.

```tsx
const x = useMotionValue(0)
const y = useMotionValue(0)
const sx = useSpring(x, { stiffness: 250, damping: 18 })  // physics, on its own loop
const sy = useSpring(y, { stiffness: 250, damping: 18 })

const onMove = (e) => {                 // fires every mousemove, but NO render
  const r = ref.current.getBoundingClientRect()
  x.set((e.clientX - (r.left + r.width/2)) * 0.4)
  y.set((e.clientY - (r.top + r.height/2)) * 0.4)
}
<m.div ref={ref} onMouseMove={onMove} onMouseLeave={() => { x.set(0); y.set(0) }} style={{ x: sx, y: sy }} />
```

`useTransform` maps one motion value to another with zero renders — great for
deriving rotation, opacity, color from a single source:

```tsx
const rotate = useTransform(x, [-100, 100], [-15, 15])
```

## Enter / exit — AnimatePresence

Animate components as they mount/unmount. The exiting element must be a direct
child and keyed.

```tsx
<AnimatePresence initial={false}>
  {open && (
    <m.div
      initial={{ opacity: 0, height: 0 }}
      animate={{ opacity: 1, height: 'auto' }}
      exit={{ opacity: 0, height: 0 }}
      transition={{ duration: 0.25 }}
    >…</m.div>
  )}
</AnimatePresence>
```

For lists that reorder/filter, key by a **stable id** (not the index) and use
`mode="popLayout"` so leaving items don't shove the layout:

```tsx
<AnimatePresence mode="popLayout">
  {items.map(it => (
    <m.div key={it.id} layout initial={{opacity:0,scale:.9}} animate={{opacity:1,scale:1}} exit={{opacity:0,scale:.9}} />
  ))}
</AnimatePresence>
```

## Shared-layout transitions — the `layout` prop

Add `layout` and framer-motion animates position/size changes with transforms
automatically (FLIP) — no manual measuring. Use it for reordering grids,
expanding cards, moving an active indicator. Pair with `layoutId` to morph one
element into another across the tree (e.g. a thumbnail expanding into a modal).

```tsx
<m.div layout className="card" />
{active && <m.div layoutId="highlight" className="ring" />}
```

Keep `layout` elements simple; deep layout trees animating at once get
expensive.

## Variants + stagger — orchestrated reveals

Variants let a parent drive children with one `animate`, and stagger them:

```tsx
const list = { show: { transition: { staggerChildren: 0.08 } } }
const item = { hidden: { y: '110%', opacity: 0 }, show: { y: 0, opacity: 1, transition: { ease: [0.16,1,0.3,1], duration: 0.8 } } }

<m.ul variants={list} initial="hidden" animate="show">
  {words.map((w,i) => <m.li key={`${w}-${i}`} variants={item}>{w}</m.li>)}
</m.ul>
```

This is the basis of kinetic type (split text into words/lines, stagger a
masked slide-up). Mask each line with `overflow:hidden` and translate the child
from `110%` to `0`.

## Gestures — declarative, GPU-friendly

`whileHover`, `whileTap`, `whileFocus`, `whileInView`, and `drag` run outside
render and animate transforms:

```tsx
<m.button whileHover={{ scale: 1.05 }} whileTap={{ scale: 0.96 }} />
<m.div drag dragConstraints={{ left: 0, right: 300 }} dragElastic={0.2} />
```

## Imperative animate() — fire-and-forget tweens

For non-style values (a counting number) or sequences, drive `animate()` and
write the result to a ref instead of state:

```tsx
useEffect(() => {
  const c = animate(0, to, { duration: 1.6, ease: [0.16,1,0.3,1],
    onUpdate: v => { if (numRef.current) numRef.current.textContent = String(Math.round(v)) } })
  return () => c.stop()
}, [inView, to])
```

## Transitions cheat-sheet

- **Spring** (`type:'spring'`, `stiffness`/`damping`/`mass`) — natural, for
  gestures and anything interruptible.
- **Tween** (`duration` + `ease`) — precise, for choreographed reveals. A nice
  cinematic ease is `[0.16, 1, 0.3, 1]` (expo-out).
- Keep continuous loops (`repeat: Infinity`) on `transform`/`opacity` only.
