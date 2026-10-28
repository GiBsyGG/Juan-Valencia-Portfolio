---
name: Portfolio Juan Valencia
description: Portfolio de una página con estética de terminal/editor de código: negro vacío, texto cian pálido, un solo acento magenta.
colors:
  magenta-cursor: "#ff00ff"
  void-black: "#09090b"
  panel-black: "#0d0d11"
  white: "#ffffff"
  cyan-mist: "#e0ffff"
  cyan-mist-muted: "rgba(224, 255, 255, 0.62)"
  cyan-mist-dim: "rgba(224, 255, 255, 0.55)"
  teal-line: "rgba(92, 225, 230, 0.34)"
  mist-line: "rgba(224, 255, 255, 0.28)"
typography:
  display:
    fontFamily: "JetBrains Mono, ui-monospace, monospace"
    fontSize: "clamp(34px, 5.2vw, 64px)"
    fontWeight: 700
    lineHeight: 1.08
    letterSpacing: "-0.02em"
  headline:
    fontFamily: "JetBrains Mono, ui-monospace, monospace"
    fontSize: "clamp(30px, 4vw, 48px)"
    fontWeight: 700
    lineHeight: 1.15
    letterSpacing: "-0.015em"
  title:
    fontFamily: "JetBrains Mono, ui-monospace, monospace"
    fontSize: "19px"
    fontWeight: 500
    lineHeight: 1.3
  body:
    fontFamily: "Inter, system-ui, sans-serif"
    fontSize: "17px"
    fontWeight: 400
    lineHeight: 1.65
  label:
    fontFamily: "JetBrains Mono, ui-monospace, monospace"
    fontSize: "13px"
    fontWeight: 400
    lineHeight: 1.4
rounded:
  sm: "2px"
spacing:
  xs: "8px"
  sm: "16px"
  md: "24px"
  lg: "56px"
  section: "clamp(96px, 14vh, 160px)"
components:
  button-solid:
    backgroundColor: "transparent"
    textColor: "{colors.magenta-cursor}"
    rounded: "{rounded.sm}"
    padding: "0 20px"
    height: "48px"
  button-solid-hover:
    backgroundColor: "{colors.magenta-cursor}"
    textColor: "{colors.void-black}"
  button-ghost:
    backgroundColor: "transparent"
    textColor: "{colors.white}"
    rounded: "{rounded.sm}"
    padding: "0 20px"
    height: "48px"
  button-ghost-hover:
    textColor: "{colors.magenta-cursor}"
  card:
    backgroundColor: "{colors.panel-black}"
    textColor: "{colors.cyan-mist}"
    rounded: "{rounded.sm}"
    padding: "24px"
  input:
    backgroundColor: "transparent"
    textColor: "{colors.cyan-mist}"
    rounded: "{rounded.sm}"
    padding: "12px 14px"
---

# Design System: Portfolio Juan Valencia

## Overview

**Creative North Star: "The Live Terminal"**

El sitio se comporta como una sesión de editor en vivo: tipografía mono en titulares, un caret que parpadea, líneas numeradas, barras de estado y un riel lateral con cursor que sigue el scroll. Todo vive sobre un negro casi absoluto; el único color con voz es el magenta, usado como el cursor de la terminal: pequeño, preciso, siempre señalando dónde está la atención.

La densidad es media y ordenada: secciones amplias separadas por filas de borde teal tenue, tarjetas planas con borde de 1 px y esquinas casi rectas. La sensación es de herramienta técnica, seca y exacta, no decorativa. El glow magenta es respuesta a la interacción, nunca ambiente.

**Key Characteristics:**
- Mono (JetBrains Mono) para estructura y títulos; Inter solo para párrafos.
- Un acento (magenta) sobre negro; el resto es cian pálido en opacidades.
- Superficies planas, bordes de 1 px, radio de 2 px.
- Glow solo en hover/foco; reveal al scroll y typing en el hero.

## Colors

Negro vacío, texto cian pálido tipo fósforo, y un único acento magenta eléctrico.

### Primary
- **Magenta Cursor** (#ff00ff): caret, enlace activo del navbar, número de sección activo, borde y glow en hover/foco, nodos y cursor del riel, botón sólido, `::selection`.

### Neutral
- **Void Black** (#09090b): fondo de página y de las tarjetas del laboratorio; texto sobre magenta relleno.
- **Panel Black** (#0d0d11): superficie de tarjetas de proyecto y del panel de editor del hero.
- **White** (#ffffff): titulares y nombre.
- **Cyan Mist** (#e0ffff): texto de cuerpo y enlaces por defecto.
- **Cyan Mist Muted** (rgba 224,255,255 / 0.62): metadatos, eyebrows, barras de estado.
- **Cyan Mist Dim** (rgba 224,255,255 / 0.55): texto terciario.
- **Teal Line** (rgba 92,225,230 / 0.34): bordes de secciones, tarjetas y navbar.
- **Mist Line** (rgba 224,255,255 / 0.28): bordes de botones e interacciones suaves.

### Named Rules
**The Cursor Rule.** El magenta marca el foco de atención actual (activo, hover, foco, cursor). Nunca se usa como relleno decorativo ni en bloques de texto.

**The No-Third-Hue Rule.** Solo existen negro, cian pálido y magenta. No se introducen otros matices.

## Typography

**Display / Headline / Title / Label:** JetBrains Mono (fallback ui-monospace)
**Body:** Inter (fallback system-ui)

**Character:** Mono para todo lo que es "estructura de código" (nombres, títulos, etiquetas, botones, metadatos); Inter relaja los párrafos largos.

### Hierarchy
- **Display** (700, clamp 34–64px, 1.08, -0.02em): titular del hero con efecto typing.
- **Headline** (700, clamp 30–48px, 1.15, -0.015em): `h2` de cada sección.
- **Title** (500, 19px): `h3` de tarjetas.
- **Body** (400, 15–18px, 1.6–1.65): párrafos; ancho máximo ~560–640px / 62ch.
- **Label** (400–500, 13–14px, mono): eyebrows, chips, pestañas, botones, barra de estado.

### Named Rules
**The Mono Skeleton Rule.** Si es estructura (título, etiqueta, botón, metadato) va en mono; si es lectura continua va en Inter.

## Layout

Una página, contenedor de `max-width: 1200px` con padding horizontal `clamp(20px, 5vw, 64px)`. Secciones con padding vertical `clamp(96px, 14vh, 160px)` separadas por `border-top` teal. Hero a dos columnas (copy + panel editor) con flex-wrap; se apila bajo ~980px. Grids de tarjetas con `repeat(auto-fit, minmax(min(100%, 300px), 1fr))` y gap 24px. Navbar sticky de 64px; breakpoint de navegación móvil en 820px (aparece botón `menú`, se oculta el riel lateral). Objetivos táctiles ≥ 44px. Navegación por anclas con `scroll-margin-top: 64px`.

## Elevation & Depth

Plano por defecto. La profundidad viene de bordes de 1 px y del salto tonal entre Void Black y Panel Black. El navbar usa fondo casi opaco con `backdrop-filter: blur(10px)`. Las sombras existen solo como respuesta de estado.

### Shadow Vocabulary
- **Hover glow – card** (`box-shadow: 0 0 0 1px rgba(255,0,255,0.35), 0 0 28px rgba(255,0,255,0.14)`): hover/foco en tarjetas.
- **Hover glow – button** (`box-shadow: 0 0 18px rgba(255,0,255,0.18)`): hover/foco en botones.
- **Rail cursor glow** (`box-shadow: 0 0 14px rgba(255,0,255,0.45)`): cursor del riel lateral.

### Named Rules
**The Flat-Until-Touched Rule.** Ningún elemento brilla en reposo; el glow magenta aparece solo con hover o foco.

## Shapes

Esquinas casi rectas (2px) en botones, tarjetas, inputs, chips y capturas. Bordes de 1 px; los estados en cola usan borde punteado. Marcadores cuadrados (6–9px) en lugar de círculos: puntos de estado, nodos del riel, caret rectangular.

## Components

### Buttons
- **Shape:** rectángulo con esquinas de 2px, alto mínimo 48px (hero), mono 13–14px.
- **Solid:** contorno y texto magenta; en hover/foco se rellena de magenta con texto Void Black.
- **Ghost:** borde Mist Line, texto blanco; en hover/foco borde y texto magenta + glow suave.
- **Menú / CV (navbar):** ghost compacto (padding 9px 14px); botón de menú de ≥ 44px de alto.

### Cards / Containers
- **Corner Style:** 2px.
- **Background:** Panel Black (proyectos) o Void Black (laboratorio).
- **Border:** 1px Teal Line; punteado para "en cola".
- **Hover/Focus-within:** borde magenta + glow de tarjeta.
- **Padding:** 24–28px. Pestaña superior mono con punto cuadrado que se vuelve magenta.

### Chips
Borde Mist Line, texto Cyan Mist, padding 3px 8px, mono 13px; variante "en cola" con borde y texto atenuados.

### Inputs / Fields
Transparentes, borde rgba(224,255,255,0.2), radio 2px, texto 14px. Foco: borde magenta. Cada campo con `label` asociado.

### Navigation
Barra sticky mono: marca con caret magenta parpadeante, enlaces numerados 01–03 (número a 55% de opacidad), enlace activo en magenta, botón de CV. Móvil: botón `menú`/`cerrar` con panel desplegable.

### Editor Panel (signature)
Panel `perfil.config.ts` en el hero: barra superior con nombre de archivo y UTF-8, cuerpo con números de línea y sintaxis (palabras clave magenta, cadenas cian al 72%, comentarios al 50%), barra de estado inferior (`⎇ main`, `Ln, Col`).

### Scroll Rail (signature)
Línea de 1px a la izquierda con nodos cuadrados que se rellenan de magenta al pasar cada sección y un cursor magenta de 9×22px que sigue el scroll. Oculto bajo 820px.

## Do's and Don'ts

### Do:
- **Do** usar magenta solo para activo, hover, foco y cursor (The Cursor Rule).
- **Do** mantener radio de 2px y bordes de 1px en todo contenedor.
- **Do** poner títulos, etiquetas y botones en JetBrains Mono; párrafos en Inter.
- **Do** construir color y fuente con las variables de `global.css` y estilos específicos en `<style>` scoped.
- **Do** respetar `prefers-reduced-motion`: typing completo, sin parpadeo, sin transiciones.
- **Do** mantener foco visible (outline magenta 2px, offset 2px) y objetivos táctiles ≥ 44px.

### Don't:
- **Don't** añadir un segundo color de acento ni rellenos magenta decorativos.
- **Don't** dar glow o sombra en reposo; solo en hover/foco.
- **Don't** usar esquinas redondeadas grandes, círculos ni gradientes de relleno.
- **Don't** poner texto largo en mono ni titulares en Inter.
- **Don't** usar rutas absolutas `/...` para assets; usar `import.meta.env.BASE_URL`.
