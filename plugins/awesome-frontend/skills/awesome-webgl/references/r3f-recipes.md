# r3f Recipes (verified, copy-paste)

Concrete snippets from a deployed Lusion-style portfolio (Next 16 + React 19 +
r3f 9 + drei 10 + postprocessing 3). All verified rendering in a real browser.

## Install
```bash
npm i three @react-three/fiber @react-three/drei @react-three/postprocessing lenis framer-motion
npm i -D @types/three
```

## 1. The client island that owns the canvas (Next ssr:false)
```tsx
"use client";
import dynamic from "next/dynamic";
import { useEffect, useState } from "react";
const Scene = dynamic(() => import("./HeroScene"), { ssr: false }); // ssr:false MUST be in a client component

export default function Experience() {
  const [mounted, setMounted] = useState(false);
  const [reduced, setReduced] = useState(false);
  useEffect(() => {
    setMounted(true);
    setReduced(window.matchMedia("(prefers-reduced-motion: reduce)").matches);
  }, []);
  return (
    <div className="fixed inset-0 z-0">
      {/* static fallback — also covers reduced-motion / no-webgl */}
      <div className="absolute inset-0 bg-[radial-gradient(50%_50%_at_50%_45%,rgba(79,57,246,.14),transparent_70%)]" />
      {mounted && !reduced && <Scene />}
    </div>
  );
}
```

## 2. Cursor + scroll reactive glass orb (the centerpiece)
```tsx
"use client";
import { Canvas, useFrame, useThree } from "@react-three/fiber";
import { Float, Environment, MeshTransmissionMaterial } from "@react-three/drei";
import { EffectComposer, Bloom, ChromaticAberration } from "@react-three/postprocessing";
import { useMemo, useRef } from "react";
import * as THREE from "three";
import type { Mesh } from "three";

const scrollP = () => (typeof window === "undefined" ? 0 : Math.min(1, window.scrollY / (window.innerHeight || 1)));

function Glass() {
  const mesh = useRef<Mesh>(null);
  const mat = useRef<any>(null); // drei impl type — keep loose
  const { pointer } = useThree();
  useFrame((_, dt) => {
    const m = mesh.current, p = scrollP();
    if (m) {
      m.rotation.y += dt * (0.1 + p * 0.4);
      m.rotation.x = THREE.MathUtils.lerp(m.rotation.x, pointer.y * 0.5 + p * 0.6, 0.06);
      m.scale.setScalar(THREE.MathUtils.lerp(m.scale.x, THREE.MathUtils.lerp(1.85, 1.1, p), 0.1));
      m.position.y = THREE.MathUtils.lerp(m.position.y, 1.15 + p * 1.4, 0.08); // shrink + drift on scroll
    }
    if (mat.current) {
      const e = Math.min(1, Math.abs(pointer.x) + Math.abs(pointer.y));
      mat.current.distortionScale = THREE.MathUtils.lerp(mat.current.distortionScale ?? 0.3, 0.25 + e * 0.5, 0.05);
    }
  });
  return (
    <Float speed={1.2} rotationIntensity={0.35} floatIntensity={0.6}>
      <mesh ref={mesh} scale={1.85}>
        <icosahedronGeometry args={[1, 12]} />
        <MeshTransmissionMaterial ref={mat} samples={4} resolution={256} transmission={1}
          roughness={0.04} thickness={1.4} ior={1.42} chromaticAberration={0.4}
          anisotropy={0.25} distortion={0.4} distortionScale={0.3} temporalDistortion={0.2} color="#fff" />
      </mesh>
    </Float>
  );
}

export default function HeroScene() {
  const ab = useMemo(() => new THREE.Vector2(0.0009, 0.0009), []);
  return (
    <Canvas style={{ position: "absolute", inset: 0 }} camera={{ position: [0, 0, 6], fov: 40 }}
      dpr={[1, 1.75]} gl={{ antialias: true, powerPreference: "high-performance" }}>
      <color attach="background" args={["#efeee9"]} />
      <ambientLight intensity={0.8} />
      <directionalLight position={[4, 5, 6]} intensity={1.4} />
      <Glass />
      <Environment preset="sunset" />{/* transmission needs an env to refract */}
      <EffectComposer>
        <Bloom intensity={0.45} luminanceThreshold={0.75} mipmapBlur />
        <ChromaticAberration offset={ab} />
      </EffectComposer>
    </Canvas>
  );
}
```
For a **chrome / liquid-metal** look instead of glass, swap the material for
`<MeshDistortMaterial color="#fff" metalness={1} roughness={0.05} distort={0.3} speed={1.8} envMapIntensity={1.6} />`.

## 3. Interactive particle field (CPU-updated, round sprites, mouse repulsion)
```tsx
function Particles({ count = 480 }) {
  const ref = useRef<THREE.Points>(null);
  const { pointer, viewport } = useThree();
  const sprite = useMemo(() => {                       // round soft point
    const c = document.createElement("canvas"); c.width = c.height = 64;
    const g = c.getContext("2d")!; const grd = g.createRadialGradient(32, 32, 0, 32, 32, 32);
    grd.addColorStop(0, "rgba(255,255,255,1)"); grd.addColorStop(1, "rgba(255,255,255,0)");
    g.fillStyle = grd; g.fillRect(0, 0, 64, 64); return new THREE.CanvasTexture(c);
  }, []);
  const { positions, base } = useMemo(() => {
    const positions = new Float32Array(count * 3), base = new Float32Array(count * 3);
    for (let i = 0; i < count; i++) {
      const r = 4 + Math.random() * 6, th = Math.random() * Math.PI * 2, ph = Math.acos(2 * Math.random() - 1);
      const x = r * Math.sin(ph) * Math.cos(th), y = r * Math.sin(ph) * Math.sin(th) * 0.6, z = r * Math.cos(ph) * 0.5;
      base.set([x, y, z], i * 3); positions.set([x, y, z], i * 3);
    }
    return { positions, base };
  }, [count]);
  useFrame((s) => {
    const geo = ref.current?.geometry; if (!geo) return;
    const t = s.clock.elapsedTime, pos = geo.attributes.position.array as Float32Array;
    const mx = pointer.x * (viewport.width / 2), my = pointer.y * (viewport.height / 2);
    for (let i = 0; i < count; i++) {
      const ix = i * 3;
      let x = base[ix] + Math.sin(t * 0.3 + base[ix + 1] * 0.5) * 0.35;   // flow
      let y = base[ix + 1] + Math.cos(t * 0.25 + base[ix] * 0.5) * 0.35;
      const z = base[ix + 2] + Math.sin(t * 0.2 + base[ix + 2] * 0.5) * 0.35;
      const dx = x - mx, dy = y - my, d = Math.hypot(dx, dy), R = 2.4;     // repulsion
      if (d < R) { const f = (1 - d / R) * 0.9; x += (dx / (d || 1)) * f; y += (dy / (d || 1)) * f; }
      pos[ix] = x; pos[ix + 1] = y; pos[ix + 2] = z;
    }
    geo.attributes.position.needsUpdate = true;
  });
  return (
    <points ref={ref}>
      <bufferGeometry><bufferAttribute attach="attributes-position" args={[positions, 3]} /></bufferGeometry>
      <pointsMaterial size={0.07} color="#4f39f6" map={sprite} alphaTest={0.01} transparent opacity={0.8} sizeAttenuation depthWrite={false} />
    </points>
  );
}
```
For tens of thousands of particles, move this to a **GPGPU FBO** (positions in a
texture, a shader updates them) — see `lusion-techniques.md`.

## 4. Robust preloader (no background-tab stall)
```tsx
"use client";
import { useEffect, useState } from "react";
import { motion, AnimatePresence } from "framer-motion";
export default function Preloader() {
  const [done, setDone] = useState(false), [n, setN] = useState(0);
  useEffect(() => {
    if (matchMedia("(prefers-reduced-motion: reduce)").matches) return setDone(true);
    const start = performance.now(), DUR = 1600; let raf = 0;
    const tick = (t: number) => {
      const p = Math.min(1, (t - start) / DUR); setN(Math.round(p * 100));
      if (p < 1) raf = requestAnimationFrame(tick); else setTimeout(() => setDone(true), 450);
    };
    raf = requestAnimationFrame(tick);
    const hard = setTimeout(() => setDone(true), 2600); // GUARANTEE reveal even if throttled
    return () => { cancelAnimationFrame(raf); clearTimeout(hard); };
  }, []);
  return (
    <AnimatePresence>
      {!done && (
        <motion.div className="fixed inset-0 z-[80] flex items-end justify-between bg-black p-10 text-white"
          exit={{ y: "-101%" }} transition={{ duration: 0.9, ease: [0.76, 0, 0.24, 1] }}>
          <span className="font-mono text-xs uppercase tracking-widest">Loading</span>
          <span className="font-mono text-[11vw] tabular-nums">{n}</span>
        </motion.div>
      )}
    </AnimatePresence>
  );
}
```
> Why: a `setInterval` counter is throttled to ~1/s in a background tab and crawls.
> rAF + a hard `setTimeout` cap guarantees the page reveals.

## 5. Lenis smooth scroll + ANIMATED anchor links (no hard jump)
```tsx
"use client";
import { useEffect } from "react";
import Lenis from "lenis";
export default function SmoothScroll() {
  useEffect(() => {
    if (matchMedia("(prefers-reduced-motion: reduce)").matches) return;
    const lenis = new Lenis({ lerp: 0.1, smoothWheel: true, syncTouch: false });
    let id = 0; const raf = (t: number) => { lenis.raf(t); id = requestAnimationFrame(raf); }; id = requestAnimationFrame(raf);
    const onClick = (e: MouseEvent) => {                       // intercept #anchor clicks → animate
      const a = (e.target as HTMLElement)?.closest?.('a[href^="#"]') as HTMLAnchorElement | null;
      const href = a?.getAttribute("href"); if (!href || href === "#") return;
      const el = document.querySelector(href); if (!el) return;
      e.preventDefault();
      lenis.scrollTo(el as HTMLElement, { duration: 1.3, easing: (t) => 1 - Math.pow(1 - t, 3) });
    };
    document.addEventListener("click", onClick);
    return () => { cancelAnimationFrame(id); document.removeEventListener("click", onClick); lenis.destroy(); };
  }, []);
  return null;
}
```
> Lenis does NOT intercept anchor links by default — native `#hash` does a hard
> jump. Intercept clicks and call `lenis.scrollTo` for an animated scroll.

## 6. Vertical sticky-stack "reveal one by one" (DOM, no WebGL)
```tsx
function Panel({ p }) {
  const ref = useRef(null);
  const { scrollYProgress } = useScroll({ target: ref, offset: ["start start", "end start"] });
  const scale = useTransform(scrollYProgress, [0, 1], [1, 0.88]);    // shrink behind as next rises
  const opacity = useTransform(scrollYProgress, [0, 0.85], [1, 0.45]);
  const imgY = useTransform(scrollYProgress, [0, 1], ["-6%", "8%"]); // image parallax in frame
  return (
    <div ref={ref} className="sticky top-0 h-screen flex items-center justify-center">
      <motion.div style={{ scale, opacity }} className="relative h-[82vh] w-[92vw] overflow-hidden rounded-2xl">
        <motion.div style={{ y: imgY }} className="absolute inset-[-8%]"><img src={p.src} className="h-full w-full object-cover" /></motion.div>
        {/* title overlay */}
      </motion.div>
    </div>
  );
}
```
Free placeholder imagery without an API key: **`https://picsum.photos/seed/<seed>/1600/1100`**
(real Unsplash-sourced photos). Use plain `<img>` (or `motion.img`) to skip
next/image remote config.
