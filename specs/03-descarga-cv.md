# SPEC 03 — Descarga del CV

> **Estado:** Aprobado
> **Depende de:** SPEC 01, SPEC 02
> **Fecha:** 2026-10-28
> **Objetivo:** Hacer que los tres botones "Descargar CV" (navbar desktop, panel móvil y Contacto) descarguen `JuanValencia_CV_Dev.pdf` servido desde el propio sitio.

## Por qué existe esta spec

La SPEC 01 dejó el placeholder `[URL-CV]` en tres enlaces y la SPEC 02 lo excluyó explícitamente ("otra spec"). El PDF ya existe en `references/CV/JuanValencia_CV_Dev.pdf`, pero `references/` no se despliega: para que GitHub Pages lo sirva debe vivir en `public/`.

## Alcance

**Dentro:**

- Copiar `references/CV/JuanValencia_CV_Dev.pdf` a `public/cv/JuanValencia_CV_Dev.pdf` (el original se conserva).
- Reemplazar `[URL-CV]` en los 3 enlaces:
  - `Navbar.astro` → `<a class="btn cv">` (desktop).
  - `Navbar.astro` → enlace del `#menu-panel` (móvil).
  - `Contacto.astro` → fila `Descargar CV` del arreglo `enlaces`.
- Descarga directa: atributo `download="JuanValencia_CV_Dev.pdf"` en los 3 enlaces.
- `href` construido con `import.meta.env.BASE_URL` (convención del proyecto para el base `/Juan-Valencia-Portfolio/`).
- Quitar la sección "Pendiente" (`[URL-CV]`) de `CLAUDE.md`.

**Fuera de alcance (specs futuras):**

- Versión del CV en inglés o varios idiomas.
- Visor/preview del PDF dentro de la página u apertura en pestaña nueva.
- Analítica de descargas.
- Cambios de diseño o copy de los botones.
- Automatizar la sincronización entre `references/CV/` y `public/cv/`.

## Modelo de datos

Esta feature no introduce estructuras de datos nuevas. Reutiliza el arreglo `enlaces` de `Contacto.astro` (SPEC 02), con un campo opcional adicional:

```ts
interface Enlace {
  prefijo: string;
  valor: string;
  url: string;
  flecha: string;
  externo: boolean;
  descarga?: string;   // nombre de archivo; si existe, el <a> lleva download={descarga}
}
```

Archivo servido: `public/cv/JuanValencia_CV_Dev.pdf` → URL `${import.meta.env.BASE_URL}cv/JuanValencia_CV_Dev.pdf`.

## Plan de implementación

1. **Archivo.** Crear `public/cv/` y copiar el PDF desde `references/CV/`. Verificar: `astro dev` sirve `/Juan-Valencia-Portfolio/cv/JuanValencia_CV_Dev.pdf`.
2. **Navbar.** En `Navbar.astro` definir en el frontmatter `const cvUrl = \`${import.meta.env.BASE_URL}cv/JuanValencia_CV_Dev.pdf\`` (el componente hoy no tiene frontmatter; añadirlo) y usarlo con `download` en el botón desktop y en el enlace del panel móvil. Verificar: ambos enlaces descargan el PDF; el panel móvil se cierra como con los demás enlaces.
3. **Contacto.** En `Contacto.astro` cambiar `url: '[URL-CV]'` por la misma ruta con `BASE_URL`, añadir `descarga: 'JuanValencia_CV_Dev.pdf'` a esa fila y renderizar `download={e.descarga}` en el `<a>`. Verificar: la fila descarga el PDF y los enlaces github/linkedin no cambian.
4. **Cierre.** Buscar `URL-CV` en `src/` (debe dar 0 resultados), quitar la sección "Pendiente" de `CLAUDE.md` y ejecutar `astro build`.

## Criterios de aceptación

- [ ] `astro build` termina sin errores ni warnings de TypeScript/Astro.
- [ ] `grep "URL-CV"` en `src/` no devuelve resultados.
- [ ] `public/cv/JuanValencia_CV_Dev.pdf` existe y es idéntico a `references/CV/JuanValencia_CV_Dev.pdf`.
- [ ] El botón "Descargar CV" del navbar (≥ 821 px) tiene `href` `…/cv/JuanValencia_CV_Dev.pdf` y `download`, y al pulsarlo el navegador guarda `JuanValencia_CV_Dev.pdf`.
- [ ] El enlace "Descargar CV" del panel móvil (≤ 820 px) hace lo mismo y el panel se cierra.
- [ ] La fila "Descargar CV" de Contacto hace lo mismo.
- [ ] Con `astro preview` (base `/Juan-Valencia-Portfolio/`) las 3 descargas responden 200, no 404.
- [ ] Los enlaces github y linkedin de Contacto conservan `target="_blank"` y `rel="noopener noreferrer"`; el CV no.
- [ ] `CLAUDE.md` ya no contiene la sección "Pendiente" del `[URL-CV]`.
- [ ] No se añadieron dependencias nuevas a `package.json`.

## Decisiones tomadas y descartadas

- **Sí:** `public/cv/JuanValencia_CV_Dev.pdf`. Astro copia `public/` tal cual al build; la subcarpeta evita mezclar con `favicon.*` y `images/`.
- **No:** `public/` raíz. Funciona, pero desordena.
- **No:** mover el PDF (quitar de `references/`). Se conserva el original como fuente; el costo es un duplicado.
- **Sí:** conservar el nombre `JuanValencia_CV_Dev.pdf`. Es el que el usuario entregó y se verá como nombre de archivo al descargar.
- **Sí:** descarga directa con `download`. Pedido del usuario ("descargar el CV"); sin visor ni pestaña nueva.
- **No:** `target="_blank"`. Sin pestaña nueva, se mantiene el sitio abierto y no hace falta `rel`.
- **Sí:** `BASE_URL` para la ruta. Convención del proyecto; una ruta `/cv/...` daría 404 bajo `/Juan-Valencia-Portfolio/`.
- **Sí:** los 3 enlaces apuntan al mismo archivo y misma constante de ruta. Una sola fuente de verdad por componente.
- **Sí:** quitar "Pendiente" de `CLAUDE.md`. Deja de ser cierto al implementar.

## Riesgos identificados

| Riesgo | Mitigación |
| --- | --- |
| `download` se ignora en algunos navegadores o con rutas cross-origin | El archivo es mismo origen, así que `download` funciona; si se ignora, el navegador abre el PDF en su visor (degradación aceptable). |
| `BASE_URL` sin `/` final genera `…Portfolio` + `cv/…` mal unido | Astro entrega `BASE_URL` con `/` final con el `base` actual; verificar en `astro preview`. |
| `references/CV/` y `public/cv/` divergen al actualizar el CV | Documentado como fuera de alcance; al actualizar el CV, reemplazar `public/cv/`. |

## Qué **no** está en esta spec

- CV en otros idiomas.
- Visor o preview del PDF, apertura en pestaña nueva.
- Analítica de descargas.
- Cambios de diseño o copy de los botones.
- Sincronización automática entre `references/` y `public/`.

Cada uno, si se aborda, va en su propia spec.
