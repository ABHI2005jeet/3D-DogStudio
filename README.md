# REACT 3D — Dogstudio Clone

> A creative 3D web experience built with React, Three.js, and GSAP — inspired by [Dogstudio](https://dogstudio.co/).

---

## ✨ What We Built

A scroll-driven, immersive 3D landing page that mimics the feel of the award-winning Dogstudio agency website. The experience features a **live 3D animated dog model** that reacts to scroll and hover events, matcap shader transitions, full-bleed project imagery, and cinematic layout — all rendered in the browser using WebGL.

---

## 🛠️ Tech Stack

| Tool | Purpose |
|---|---|
| **React 19** | UI component tree & rendering |
| **Three.js** | Low-level 3D engine (WebGL) |
| **React Three Fiber (R3F)** | React renderer for Three.js scenes |
| **@react-three/drei** | R3F helpers — `useGLTF`, `useTexture`, `useAnimations`, `OrbitControls` |
| **GSAP + ScrollTrigger** | Scroll-scrubbed animations & timeline control |
| **@gsap/react** | `useGSAP` hook for safe GSAP integration inside React |
| **GLSL / Shaders** | Custom fragment shader injected via `onBeforeCompile` |
| **Vite** | Build tool & dev server |
| **Oxlint** | Blazing-fast JavaScript / JSX linter |

---

## ⚛️ React Concepts Used

- **Functional Components** — entire app is composed of function components (`App`, `Dog`)
- **`useRef`** — holds mutable refs to the 3D model and shader uniforms without causing re-renders
- **`useEffect`** — sets up DOM `mouseenter` / `mouseleave` event listeners for hover-driven matcap swaps
- **`useState`** (imported, available for future use)
- **`StrictMode`** — wraps the app in `main.jsx` for catching side-effect bugs during development
- **JSX Fragments** (`<>...</>`) — used throughout to avoid extra DOM wrappers

---

## 🎨 3D & Shader Features

### Model & Scene
- **GLB Model** — a compressed `.drc.glb` dog model loaded with `useGLTF`
- **Skeletal Animation** — the dog's idle animation is played via `useAnimations` + Three.js `AnimationMixer`
- **Matcap Materials** — 20 matcap textures (`mat-1.png` → `mat-20.png`) loaded with `useTexture`; applied as `THREE.MeshMatcapMaterial`
- **Normal Maps** — separate normal maps for the dog body and branches mesh, loaded and applied for surface detail
- **Scene Traversal** — `model.scene.traverse()` to apply different materials to `DOG` vs branch meshes

### Custom GLSL Shader (`onBeforeCompile`)
The dog's matcap material is extended at the GLSL level using Three.js's `onBeforeCompile` hook:

```glsl
// Injected uniforms
uniform sampler2D uMatcapTexture1;
uniform sampler2D uMatcapTexture2;
uniform float uProgress;

// Smooth wipe transition between two matcap textures
float progress = smoothstep(
  uProgress - 0.2,
  uProgress,
  (vViewPosition.x + vViewPosition.y) * 0.5 + 0.5
);

vec4 matcapColor = mix(matcapColor2, matcapColor1, progress);
```

This creates a **smooth, directional wipe** between two matcap textures — driven by GSAP on hover.

### Camera & Renderer
- `useThree` — accesses the R3F camera, scene, and WebGL renderer to configure:
  - `camera.position.z`
  - `gl.toneMapping = THREE.ReinhardToneMapping`
  - `gl.outputColorSpace = THREE.SRGBColorSpace`

---

## 🎬 GSAP Animations

### Scroll-Scrubbed Timeline (`ScrollTrigger` + `scrub`)
Triggered from `#section-1` to `#section-4`, the dog:
1. Moves forward and up (`position.z`, `position.y`)
2. Tilts on the X axis (`rotation.x`)
3. Rotates a full 180° on Y and shifts left (using a named label `"third"` for parallel tweens)

```js
gsap.timeline({
  scrollTrigger: {
    trigger: "#section-1",
    endTrigger: "#section-4",
    start: "top top",
    end: "bottom bottom",
    scrub: true        // ties animation progress directly to scroll position
  }
})
```

### Hover-Driven Matcap Swap
Each portfolio title in section 2 triggers a GSAP tween on `uProgress` (0 → 1), animating the GLSL `smoothstep` wipe between two matcap textures, then resetting for the next transition — creating a seamless color-shift effect on the 3D dog.

---

## 🖼️ CSS Techniques

- **`:has()` selector** — modern CSS to reactively show/hide project images and the canvas background based on which title is hovered, with zero JavaScript:
  ```css
  main:has(#section-2 .title[img-title="tomorrowland"]:hover) .images #tomorrowland {
    opacity: 1;
  }
  ```
- **Fixed `<Canvas>`** — the R3F canvas is `position: fixed` behind the HTML sections, letting the 3D scene persist through scrolling
- **Nested CSS** — used throughout `App.css` (modern CSS nesting syntax)
- **Decorative diagonal lines** — thin rotated `div`s with `rgba` red for the branded line aesthetic

---

## 📁 Project Structure

```
REACT_3D/
├── public/
│   ├── models/
│   │   └── dog.drc.glb          # Compressed 3D dog model
│   ├── matcap/
│   │   └── mat-1.png … mat-20.png  # 20 matcap textures
│   ├── background-l.png
│   ├── branches_diffuse.jpeg    # Branch mesh texture
│   ├── branches_normals.jpeg    # Branch normal map
│   ├── dog_normals.jpg          # Dog body normal map
│   └── *.png                    # Project portfolio images
├── src/
│   ├── components/
│   │   └── Dog.jsx              # 3D scene component (model, shaders, GSAP)
│   ├── App.jsx                  # Layout: sections, nav, Canvas mount
│   ├── App.css                  # All styles (nested CSS)
│   └── main.jsx                 # React entry point
├── .oxlintrc.json               # Oxlint configuration
├── vite.config.js
└── package.json
```

---

## 🚀 Getting Started

```bash
# Install dependencies
npm install

# Start dev server
npm run dev

# Lint
npm run lint

# Build for production
npm run build
```

---

## 🙏 Inspiration

[Dogstudio](https://dogstudio.co/) — a multidisciplinary creative studio at the intersection of art, design, and technology.
