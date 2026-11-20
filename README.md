# Scroll-Driven 3D Story — Agent Skill

An [Agent Skill](https://agentskills.io) (`SKILL.md`) that teaches AI coding agents to choreograph 3D scenes with page scroll: scroll-linked camera moves, section-by-section storytelling, pinned heroes, smooth scrolling (Lenis), and scroll-progress-driven shaders — the signature architecture of top-tier AI websites.

## What it covers

- **One fixed canvas, scroll drives a number** — the core architecture
- Storyboard-first workflow: map scroll ranges → scene beats before coding
- Camera choreography (CatmullRom paths, eased progress)
- Lenis smooth scroll without hijacking native scroll
- DOM + 3D sync from a single source of truth
- Mobile & iOS Safari pitfalls, `prefers-reduced-motion` and no-WebGL fallbacks

## Install

```bash
# All projects (user scope)
cp -r scroll-driven-3d-story ~/.agents/skills/

# Or single project
cp -r scroll-driven-3d-story .claude/skills/
```

## Use it

Ask naturally: *"make the camera move as I scroll"*, *"scroll-driven 3D storytelling sections"*, *"the 3D scene should change per section"* — triggers on scroll animation, camera choreography, GSAP ScrollTrigger, Lenis requests.

## License

MIT
