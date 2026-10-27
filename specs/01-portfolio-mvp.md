# SPEC 01 — Portfolio MVP (single page)

> **Estado:** Borrador
> **Depende de:** ninguna
> **Fecha:** 2026-10-27
> **Objetivo:** Implementar en Astro la página única del portfolio diseñada en Claude Design (navbar, hero, proyectos, laboratorio, contacto, footer) con la estética terminal/neón del diseño.

## Por qué existe esta spec

El repo solo tiene el scaffold de Astro (`src/pages/index.astro` con un `<h1>`). El diseño de referencia vive en el artifact `1eabde8e-bc30-409c-a6af-8b95cfb4dce4` (`project/Main.dc.html`, 1440 px de ancho, fluido, responsive). Esta spec lo traduce a componentes Astro sin añadir dependencias.

## Alcance

**Dentro:**

- Una sola página `/` en español (`lang="es"`) con 4 secciones: `hero`, `proyectos`, `laboratorio`, `contacto`, más navbar sticky y footer.
- Tokens de diseño globales (colores, fuentes) y estilos scoped por componente.
- Fuentes Inter y JetBrains Mono vía Google Fonts (`<link>` en el layout, como en el diseño).
- Interactividad con JS vanilla en `<script>` de Astro:
  - Efecto typing del titular del hero.
  - Riel de scroll lateral con cursor y nodos por sección.
  - Reveal al scroll de proyectos, laboratorio y contacto.
  - Resaltado del enlace activo del navbar y del número de sección.
  - Menú móvil (botón + panel) bajo 820 px.
- Respeto de `prefers-reduced-motion` (typing completo al instante, sin transiciones, sin parpadeo del caret).
- Contenido placeholder tal cual el diseño (`[Nombre del proyecto 01]`, `[tu@email.com]`, `[URL-CV]`, etc.).
- Formulario de contacto solo como UI, sin envío.
- Accesibilidad del diseño: landmarks, `aria-label`, `aria-expanded`, `label`/`input`, foco visible, objetivos táctiles ≥ 44 px.

**Fuera de alcance (specs futuras):**

- Envío real del formulario (Formspree, endpoint propio, mailto con prefill).
- Contenido real (nombre, proyectos, capturas, CV, enlaces).
- Páginas de caso de estudio (`leer_caso`) y de preview de laboratorio.
- Content collections / datos de proyectos externalizados.
- i18n, modo claro, SEO avanzado (OG, sitemap), analítica.
- Instalar Tailwind, React o cualquier integración de Astro.
- Autoalojar las fuentes.

## Modelo de datos

Esta feature no introduce estructuras de datos persistentes. Solo tokens de diseño en `src/styles/global.css`:

```css
:root {
  --bg: #09090b;
  --surface: #0d0d11;
  --fg: #ffffff;
  --fg-soft: #e0ffff;
  --fg-muted: rgba(224, 255, 255, 0.62);
  --fg-dim: rgba(224, 255, 255, 0.55);
  --accent: #ff00ff;
  --border: rgba(92, 225, 230, 0.34);
  --border-soft: rgba(224, 255, 255, 0.28);
  --font-sans: "Inter", system-ui, sans-serif;
  --font-mono: "JetBrains Mono", ui-monospace, monospace;
}
```

Convenciones:

- Contenedor: `max-width: 1200px`, padding horizontal `clamp(20px, 5vw, 64px)`.
- Radio de borde `2px`; bordes de 1 px con `--border`.
- Breakpoint del navbar: `820px`.
- Secciones identificadas por `id`: `hero`, `proyectos`, `laboratorio`, `contacto`.

## Plan de implementación

1. Crear `src/styles/global.css` con los tokens, reset mínimo (`body{margin:0}`), `::selection`, `html{scroll-behavior:smooth}`, estilo de foco y regla `prefers-reduced-motion`. Crear `src/layouts/Layout.astro` (html `lang="es"`, meta, `<link>` de Google Fonts, importa `global.css`, `<slot />`). Hacer que `src/pages/index.astro` use el layout. Verificar: `astro dev` muestra fondo oscuro sin errores.
2. Crear `src/components/Navbar.astro` (marca `~/[tu-nombre]_`, enlaces 01–03, botón `./cv.pdf`, botón de menú y panel móvil, sin JS aún). Verificar: se ve sticky y los anclas navegan.
3. Crear `src/components/Hero.astro` (eyebrow, `h1` con titular completo estático, párrafo, dos CTAs, panel `perfil.config.ts`). Verificar: layout de dos columnas en escritorio y apilado en móvil.
4. Crear `src/components/Proyectos.astro` (cabecera de sección, tarjeta destacada con diagrama y `dl`, dos tarjetas 02 y 03). Verificar: grid responsive y hover/foco magenta.
5. Crear `src/components/Laboratorio.astro` (3 tarjetas, la tercera con borde punteado y estado `en cola`). Verificar: sin overflow horizontal a 390 px.
6. Crear `src/components/Contacto.astro` (texto, botón mailto, lista de enlaces, formulario solo UI) y `src/components/Footer.astro`. Verificar: `label` asociados a sus `input`, botón `type="button"`.
7. Añadir `<script>` en `Navbar.astro` para abrir/cerrar el menú móvil (actualiza `aria-expanded` y la etiqueta `menú`/`cerrar`; se cierra al elegir un enlace).
8. Añadir `src/scripts/typing.ts` (o `<script>` en `Hero.astro`) con el efecto typing: texto reservado con una `span` invisible para evitar layout shift, caret parpadeante; con reduced-motion muestra el titular completo.
9. Añadir riel de scroll en `Layout.astro` (línea fija a la izquierda, cursor magenta, nodos por sección) y su `<script>` con `requestAnimationFrame` en `scroll`/`resize`; oculto bajo 820 px. El mismo script marca la sección activa en navbar y eyebrow, y activa el reveal (clase al entrar en el 88 % del viewport, vía `IntersectionObserver` o medición equivalente).
10. Limpiar el scaffold: eliminar contenido sobrante de `index.astro`, ajustar `<title>` y `lang`, y revisar `public/favicon.svg` solo si no encaja. Ejecutar `astro build`.

## Criterios de aceptación

- [ ] `astro build` termina sin errores ni warnings de TypeScript/Astro.
- [ ] `/` renderiza navbar, hero, proyectos, laboratorio, contacto y footer en ese orden.
- [ ] Los enlaces del navbar y los CTAs llevan a `#proyectos`, `#laboratorio`, `#contacto` y `#hero` con scroll suave.
- [ ] El navbar permanece visible al hacer scroll (sticky) y resalta en magenta la sección activa.
- [ ] A 1440 px el hero muestra dos columnas; a 390 px se apila sin scroll horizontal.
- [ ] A ≤ 820 px se oculta la lista de enlaces y aparece el botón `menú`; al pulsarlo se abre el panel, `aria-expanded` pasa a `true` y la etiqueta cambia a `cerrar`.
- [ ] Elegir un enlace del panel móvil cierra el panel.
- [ ] El titular del hero se escribe carácter a carácter y no provoca cambios de altura del bloque durante la animación.
- [ ] Con `prefers-reduced-motion: reduce` el titular aparece completo al cargar, el caret no parpadea y las secciones aparecen sin transición.
- [ ] El riel lateral muestra un cursor magenta que sigue el scroll y 4 nodos que se rellenan al pasar cada sección; no se muestra a ≤ 820 px.
- [ ] Las secciones proyectos, laboratorio y contacto entran con fade/translate la primera vez que se ven y no se re-ocultan.
- [ ] Hover y foco de teclado en tarjetas y botones aplican borde magenta con glow.
- [ ] Cada `input`/`textarea` del formulario tiene un `label` asociado por `for`/`id` y el botón de enviar no recarga ni navega la página.
- [ ] Inter y JetBrains Mono se cargan desde Google Fonts y se aplican a texto sans y mono respectivamente.
- [ ] No se añadieron dependencias nuevas a `package.json`.
- [ ] Los colores, espaciados y tipografías coinciden con `project/Main.dc.html` al compararlos lado a lado en escritorio y móvil.

## Decisiones tomadas y descartadas

- **Sí:** componentes `.astro` por sección. Mantenible y reutilizable; el diseño en un solo archivo es un artefacto del editor, no una estructura a copiar.
- **No:** todo en `index.astro`. Difícil de mantener al crecer el contenido real.
- **Sí:** CSS scoped por componente + tokens en `global.css`. Sin dependencias y idiomático en Astro.
- **No:** Tailwind. No está instalado y traducir los estilos inline del diseño a utilidades no aporta valor en el MVP.
- **Sí:** JS vanilla en `<script>` de Astro. Sitio estático, la lógica es pequeña (typing, scroll, menú).
- **No:** isla React. Añade integración y peso para ~100 líneas de lógica.
- **Sí:** formulario solo UI. El envío real tiene decisiones propias (proveedor, estados de éxito/error, anti-spam) y merece su spec.
- **No:** formulario con `mailto:`. Experiencia pobre y sin resolver el envío real; el contacto funcional es el enlace de correo.
- **Sí:** mantener los placeholders entre corchetes del diseño. El contenido real se rellena después.
- **Sí:** fuentes por `<link>` de Google Fonts, igual que el diseño. Simple para el MVP; autoalojar queda para después.
- **Sí:** estilos inline del diseño pasan a clases CSS. Los inline eran exigencia del editor de Claude Design.

## Riesgos identificados

| Riesgo | Mitigación |
| --- | --- |
| Layout shift durante el typing | Reservar el texto completo con una `span` invisible tras el texto escrito (como el diseño). |
| Cálculo del riel desfasado al cargar fuentes o cambiar altura | Recalcular en `scroll`, `resize` y tras `load`/`document.fonts.ready`. |
| `backdrop-filter` no soportado | El fondo del navbar ya es casi opaco (`rgba(9,9,11,0.86)`); degrada bien. |
| Contraste de textos atenuados | Los valores `--fg-muted` y `--fg-dim` del diseño se mantienen; revisar contraste antes de dar por cerrado el MVP. |
| Google Fonts bloquea render o está caído | `display=swap` y fallbacks `system-ui` / `ui-monospace`. |

## Qué **no** está en esta spec

- Envío real del formulario de contacto.
- Contenido real: nombre, proyectos, capturas, CV, enlaces sociales.
- Páginas de detalle de proyectos o de laboratorio.
- i18n, modo claro, SEO avanzado, analítica.
- Tailwind, React u otras integraciones de Astro.

Cada uno, si se aborda, va en su propia spec.
