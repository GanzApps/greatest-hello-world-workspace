# WebGL/Canvas Does Not Inherit CSS

## Rule

Any visual property defined in CSS — fonts, gradients, colors, custom properties — has **zero effect** inside a WebGL or canvas rendering context. Assets must be passed explicitly as GPU-compatible inputs.

## Why

The browser CSS engine and the WebGL pipeline are entirely separate. CSS applies to DOM elements. A `<canvas>` element draws its own pixels, completely bypassing the DOM styling system.

## What this affects

| CSS concept | WebGL/canvas equivalent |
|---|---|
| `font-family` / CSS variable font | Direct font file URL (`.ttf`, `.woff2`) passed to the renderer |
| `linear-gradient()` | Shader uniform, canvas texture (`CanvasTexture`), or vertex colors |
| CSS custom properties (`--color-primary`) | Raw hex or `vec3` value passed as uniform or prop |
| `@keyframes` / CSS animation | `useFrame` loop, shader time uniform, or animation library with WebGL support |
| `opacity` / `rgba()` | Material `transparent`, `opacity`, or `alphaTest` property |

## How to apply

Before implementing any frontend ticket that uses a canvas/WebGL renderer (Three.js, R3F, PixiJS, Babylon.js, etc.):

1. List every visual property from the design: font, color, gradient, animation
2. For each — confirm it is passed as a raw asset or uniform, **not** a CSS reference
3. If the design uses a CSS-only effect (e.g. `background-clip: text` gradient), identify the WebGL equivalent before writing code

## Examples

```tsx
// WRONG — CSS variable, ignored in WebGL
<Text fontFamily="var(--font-space-grotesk)" />

// CORRECT — direct font file
<Text font="/fonts/SpaceGrotesk-Bold.ttf" />
```

```tsx
// WRONG — CSS gradient, invisible in canvas
<mesh style={{ background: "linear-gradient(#7C3AED, #06B6D4)" }} />

// CORRECT — canvas texture passed as material map
const tex = useMemo(() => makeGradientTexture("#7C3AED", "#06B6D4"), []);
<meshBasicMaterial map={tex} />
```
