---
name: scroll-driven-3d-story
description: "Choreograph 3D scenes with page scroll: scroll-linked camera moves, section-by-section scene storytelling, pinned heroes, smooth scrolling, and scroll-progress-driven shaders/animations. Use whenever the user mentions scroll animations, scroll-driven scenes, camera movement while scrolling, storytelling sections, sticky/pinned 3D canvases, scroll progress, GSAP ScrollTrigger, Lenis, or wants the website narrative to unfold as you scroll. Pair with threejs-fundamentals, threejs-animation, and ai-website-aesthetics."
---

# Scroll-Driven 3D Storytelling

The signature of top-tier AI websites: the page scrolls, the 3D scene *tells the story* — camera glides, models assemble, particles re-form. This skill is the architecture for that.

## Architecture: one fixed canvas, scroll drives a number

The canvas is `position: fixed` behind the DOM. Scroll never moves the canvas — it moves a **progress value** the scene interpolates. Everything else is implementation detail.

```js
// DOM scrolls normally; we only read, never hijack.
let scrollProgress = 0;
addEventListener('scroll', () => {
  scrollProgress = scrollY / (document.body.scrollHeight - innerHeight);
}, { passive: true });
```

In the RAF loop, interpolate scene state from `scrollProgress`. This is the whole game: build a **storyboard** (below), then write an interpolator per beat.

## The storyboard (always make one first)

Before code, map scroll ranges to scene states in comments or a table:

```
0.00–0.15  hero:    model floats center, slow spin, camera z=8
0.15–0.35  feature: camera dollies to x=-4, model splits into 3 parts
0.35–0.60  tech:    camera orbits 90°, particles swarm in, wireframe overlay fades in
0.60–0.80  proof:   camera pulls back, scene dims, DOM cards fade over canvas
0.80–1.00  CTA:     model reassembles center, glow blooms, camera z=5
```

Implement each beat as `(start, end, apply(t))` pairs; clamp progress into each range and ease `t` before applying. Interpolate **camera position, rotation, and uniform values** between keyframed states — never teleport mid-beat unless intentionally snapping.

## Smooth scroll: Lenis, not scroll-hijacking

Use Lenis for the inertia feel; it still drives native scroll (accessibility-safe):

```js
const lenis = new Lenis({ lerp: 0.1 });
function raf(time) { lenis.raf(time); update3D(); requestAnimationFrame(raf); }
requestAnimationFrame(raf);
```

- Never block native scrolling (`overflow: hidden` on body) — kills keyboard/mobile users and SEO.
- If using GSAP ScrollTrigger instead of manual progress: still keep the canvas fixed and drive scene state, don't let ScrollTrigger transform the canvas.

## Camera paths

For multi-beat moves, define a `THREE.CatmullRomCurve3` through keyframed positions and sample it by eased progress — smoother than lerping pairs of points. Look-at targets get their own curve. Ties into threejs-animation for easing/curve details.

## DOM + 3D sync

- Section text is normal DOM; give it `mix-blend-mode` or translucent panels over the canvas.
- Sync section reveals with scene beats: trigger DOM animations (GSAP or CSS) from the same `scrollProgress` ranges — one source of truth.
- Anchor 3D labels to screen space by projecting world positions each frame; hide when behind camera.

## Performance & mobile

- Keep the canvas at one DPR (cap 1.5–2); scroll-linked scenes re-render every frame regardless of scroll — drop other per-frame work.
- Pause the RAF render when the tab or scene is far off-screen (`document.visibilityState`, IntersectionObserver on sections).
- Mobile: reduce beats (not scroll length), lower particle counts, disable postprocessing (see threejs-performance and threejs-postprocessing skills).
- iOS Safari: the address bar resize changes `innerHeight` mid-scroll — debounce resize, prefer `visualViewport`-aware height for progress math if beats look jumpy.

## Accessibility & fallbacks

- Honor `prefers-reduced-motion`: render the final assembled state as a static scene, skip camera choreography; scroll stays normal.
- 3D-off fallback (weak GPU / `failsIf`) : gradient background + same DOM story. The story must survive without WebGL.

## Common mistakes

- Hijacking scroll events or wheel — the #1 reason "premium" sites feel broken.
- Reading `scrollHeight` per frame (layout thrash) — cache on resize.
- Applying raw linear progress to camera moves — always ease (slow-in-out) or it feels mechanical.
- Many ScrollTriggers each mutating the same scene properties — they fight; route everything through one progress value.
- Forgetting `lenis.raf(time)` in the loop → scroll feels dead.

## Related skills

- threejs-animation — easing, curves, morphing
- ai-website-aesthetics — the look of the beats
- threejs-postprocessing — bloom ramps between beats
- 3d-hero-interactions — the hero beat's click/hover behavior
