---
tags:
  - github
  - ai
  - 3d
  - skill
datum: 2026-03-26
szint: "🧱 Scout"
url: https://github.com/CloudAI-X/threejs-skills
kapcsolodo:
  - "[[frontend/threejs-es-3d-a-weben|Three.js]]"
  - "[[toolbox/claude-code|Claude Code]]"
  - "[[toolbox/claude-code-skills-es-plugins|Claude Code Skills]]"
  - "[[_moc/moc-ai-tooling|MOC - AI Tooling]]"
---

# threejs-skills

**Kategória:** `Claude Code skill gyűjtemény` / `3D`
**URL:** https://github.com/CloudAI-X/threejs-skills

---

## Mi ez és mire jó?

> [!tldr] Egy mondatban
> 10 darab [[frontend/threejs-es-3d-a-weben|Three.js]]-specifikus [[toolbox/claude-code|Claude Code]] skill fájl, amik a helyes API-referenciákat, kód-mintákat és performance tippeket adják meg Claude-nak 3D fejlesztéshez.

A [[toolbox/claude-code|Claude Code]] alapesetben ismeri a [[frontend/threejs-es-3d-a-weben|Three.js]]-t, de a specifikus API-felületek (konstruktor-paraméterek, shader uniform-ok, loader konfigok) terén gyakran hallucináll. Ezek a skill-ek **pontos, auditált Three.js r160+ referenciát** adnak a `.claude/skills/` mappába.

| Skill | Mire jó |
|---|---|
| **threejs-fundamentals** | Scene, Camera, Renderer, Object3D hierarchia |
| **threejs-geometry** | Beépített formák, BufferGeometry, instancing |
| **threejs-materials** | PBR, shader material-ok, standard/phong |
| **threejs-lighting** | Fénytípusok, árnyékok, environment lighting |
| **threejs-textures** | UV mapping, environment map, render target |
| **threejs-animation** | Keyframe, skeletal, morph target animáció |
| **threejs-loaders** | GLTF/GLB loading, Draco tömörítés, caching |
| **threejs-shaders** | GLSL alapok, ShaderMaterial, uniform-ok |
| **threejs-postprocessing** | Bloom, DOF, EffectComposer, custom pass |
| **threejs-interaction** | Raycasting, kamera kontrol, egér/touch input |

---

## Mikor hasznos?

- **[[frontend/threejs-es-3d-a-weben|Three.js]] projekt [[toolbox/claude-code|Claude Code]]-dal** - a skill-ek drasztikusan csökkentik a hallucinációt API hívások és shader kód generálásakor
- **Landing page 3D elemekkel** - ha "wow faktor" kell a weboldalra
- **Inspiráció skill-íráshoz** - jól strukturált skill fájlok (frontmatter, Quick Start, Core Concepts, Performance Tips), mintának használható saját skill-ekhez

### Mikor NE használd

- Ha nem [[frontend/threejs-es-3d-a-weben|Three.js]]-sel dolgozol - react-three-fiber-re (R3F) ezek részben alkalmazhatók, de nem R3F-specifikusak
- Ha egyszeri 3D feladatod van - 10 skill telepítése overkill, inkább kérj ad-hoc segítséget

---

## Telepítés

```bash
git clone https://github.com/CloudAI-X/threejs-skills.git
cp -r threejs-skills/.claude/skills/* .claude/skills/
```

---

## AI-natív fejlesztés

A skill-ek a `.claude/skills/` mappába mennek. Kontextus-alapú automatikus betöltéssel működnek: ha Three.js scene-t kérsz, a `threejs-fundamentals` töltődik be, ha GLTF modellt akarsz, a `threejs-loaders`.

> [!tip] Hogyan használd AI-val
> "Ez egy [[frontend/nextjs|Next.js]] projekt [[frontend/threejs-es-3d-a-weben|Three.js]]-szel, a .claude/skills/-ben vannak Three.js skill-ek telepítve." - így Claude tudni fogja, hogy pontos API referenciákat kaphat a skill-ekből ahelyett, hogy memóriából próbálna konstruktor-paramétereket felidézni.

---

## Kapcsolódó

- [[frontend/threejs-es-3d-a-weben|Three.js]] - mikor éri meg Three.js-t használni (döntési kontextus)
- [[toolbox/claude-code|Claude Code]] - az eszköz amihez a skill-ek készültek
- [[toolbox/claude-code-skills-es-plugins|Claude Code Skills]] - a skill rendszer amibe integrálódik
