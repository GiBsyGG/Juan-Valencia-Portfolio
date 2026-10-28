# SPEC 02 — Contenido real del portfolio

> **Estado:** Aprovado
> **Depende de:** SPEC 01
> **Fecha:** 2026-10-27
> **Objetivo:** Reemplazar los placeholders del portfolio por el contenido real de `references/personal-data.md` (nombre, stack, 3 proyectos, 2 experimentos de laboratorio y enlaces de contacto), sin cambiar el diseño ni añadir dependencias.

## Por qué existe esta spec

La SPEC 01 dejó la página completa pero con texto entre corchetes (`[Nombre del proyecto 01]`, `[tu@email.com]`, etc.). El usuario entregó `references/personal-data.md` como **única fuente** de contenido; esta spec lo traduce a los componentes existentes. Lo que el archivo no trae no se inventa: se elimina o se deja como estaba si pertenece a otra spec.

## Alcance

**Dentro:**

- **Nombre** (`Juan Valencia`): marca del navbar (`~/juan-valencia_`), footer (`© 2026 Juan Valencia — …`) y título por defecto de `Layout.astro` (`~/juan-valencia — Portfolio`).
- **Hero** (panel `perfil.config.ts`): sustituir `stack: ["[LENGUAJE]", "[MOTOR]"]` por `stack` (8 ítems) y `motores` (2 ítems); eliminar la línea `base` (ciudad, país).
- **Proyectos** (3 tarjetas): nombre, texto técnico, `patrón`, `stack`, `rol` e imagen de cada proyecto.
  - Destacado: Berracos Espantos. Secundarios: La Aventura Unal y Dead's Loot.
  - La imagen reemplaza el diagrama placeholder (destacado) y el recuadro `[captura / diagrama]` (secundarios).
  - El tag `[GÉNERO · PLATAFORMA]` pasa a `Videojuego · PC` / `Videojuego · Android` según el proyecto.
  - Se elimina la nota `[Reemplazar por captura o diagrama real]`.
  - Botón `repo` → `Jugar`, enlazado al `itchUrl` del proyecto (itch.io). Berracos Espantos y Dead's Loot lo tienen; La Aventura Unal no, así que no muestra botón `Jugar`.
  - Botón `leer_caso` oculto en las 3 tarjetas (los casos de estudio son otra spec).
- **Laboratorio** (3 tarjetas): Pulse Arkade (`en construcción`) y dos tarjetas Por Definir (`en cola`, estilo `queued`). Se elimina la tarjeta `prototipo`. Sin botón `ver_preview` ni "último commit".
- **Contacto**: email `jcval.dev@gmail.com` (texto y `mailto:`), `github/GiBsyGG` y `linkedin/juan-valencia` con sus URLs reales. Los enlaces externos abren con `target="_blank" rel="noopener noreferrer"`.
- Corregir erratas obvias del archivo (`reutilzable` → `reutilizable`).

**Fuera de alcance:**

- **CV** (`./cv.pdf` en navbar y panel móvil, `descargar/cv.pdf` en contacto): queda con el placeholder `[URL-CV]` hasta su propia spec.
- Páginas de caso de estudio y botón `leer_caso` (spec futura).
- URL de itch.io de La Aventura Unal (se añade después; el botón `Jugar` aparece solo).
- Preview/URL de los experimentos de laboratorio y "último commit" automático.
- Envío real del formulario de contacto.
- Cambios de diseño, copy de secciones (títulos `h2`, `lead`, hero), SEO, i18n.
- Content collections o archivos de datos externos.
- Optimización de imágenes con `astro:assets` / `<Image>`.

## Modelo de datos

Sin persistencia ni archivos nuevos. Los datos viven en el frontmatter de cada componente, como ya hace la SPEC 01 con `secundarios`, `experimentos` y `enlaces`.

**`Proyectos.astro`**

```ts
interface Proyecto {
  n: string;            // '01' | '02' | '03' → proyecto_NN.md
  nombre: string;
  resumen: string;      // texto de "Problema técnico que resuelve el proyecto"
  patron: string[];     // se muestra unido con ' · '
  stack: string[];      // se muestra unido con ' · '
  rol: string;
  genero: string;       // tag de la pestaña: `${genero} · ${plataforma}`
  plataforma: string;
  imagen: string;       // ruta bajo /images/projects/
  alt: string;
  itchUrl?: string;     // si falta, no hay botón "Jugar"
}
```

| n | nombre | patron | stack | rol | género · plataforma | itchUrl | imagen |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 01 | Berracos Espantos | MVC, HSM, Object Pooling, ScriptableObjects | Unity, C#, Claude Code + Unity CLI | Desarrollador | Videojuego · PC | https://monte-estudio.itch.io/berracos-espantos | `/images/projects/Berracos_Espantos.png` |
| 02 | La Aventura Unal | MVC, ScriptableObjects, Object Pooling | Unity, C# | Desarrollador | Videojuego · Android | — | `/images/projects/La_Aventura_UNAL.png` |
| 03 | Dead's Loot | Random Walk, Binary Space Partitioning, ScriptableObjects | Unity, C# | Desarrollador | Videojuego · PC | https://gibsygg.itch.io/deads-loot | `/images/projects/Deads_Loot.png` |

`resumen` se copia de `personal-data.md` (texto literal salvo erratas).

**`Laboratorio.astro`**

```ts
interface Experimento {
  slug: string;         // 'lab/pulse-arkade' | 'lab/por-definir'
  estado: 'en construcción' | 'en cola';
  nombre: string;
  exploracion: string;
  stack: string;
  queued?: boolean;     // borde punteado + chip atenuado
  icono: string[];
}
```

| slug | estado | nombre | exploracion | stack |
| --- | --- | --- | --- | --- |
| `lab/pulse-arkade` | en construcción | Pulse Arkade | Biblioteca de juegos retro para explorar el uso de harness de IA, usando el patrón orquestador-trabajadores en el flujo de desarrollo de un videojuego. | Next.js |
| `lab/por-definir` | en cola (`queued`) | Por Definir | Aún en construcción. | Por Definir |
| `lab/por-definir` | en cola (`queued`) | Por Definir | Aún en construcción. | Por Definir |

La línea de estado de cada tarjeta pasa a `stack: <stack>`.

**`Hero.astro`** (panel, 11 líneas)

```ts
export const dev = {
  rol:     "Software Developer",
  foco:    ["arquitectura", "sistemas"],
  stack: [
    "C#", "Spring Boot", "React", "Angular",
    "Flutter", "Python", "Express.js", "Next.js",
  ],
  motores: ["Unity", "Godot"],
  estado:  "disponible",
};
```

El gutter pasa a `1…11` y la barra de estado a `Ln 11, Col 3`. `rol`, `foco` y `estado` ya no son placeholders y no se tocan.

**`Contacto.astro`**

| prefijo | valor | url |
| --- | --- | --- |
| `github/` | GiBsyGG | https://github.com/GiBsyGG |
| `linkedin/` | juan-valencia | https://www.linkedin.com/in/juan-valencia-a13161234/ |
| `descargar/` | cv.pdf | `[URL-CV]` (sin cambios, otra spec) |

## Plan de implementación

1. **Nombre.** Reemplazar `[tu-nombre]` en `Navbar.astro` (marca) y `Layout.astro` (título por defecto) por `juan-valencia`, y `[Tu Nombre]` en `Footer.astro` por `Juan Valencia`. Verificar: pestaña, navbar y footer muestran el nombre.
2. **Hero.** Actualizar `pre`, gutter (`1…11`) y `Ln 11, Col 3` en `Hero.astro` según el modelo. Verificar: sin scroll horizontal del panel a 1440 px y 390 px; sin `[LENGUAJE]`, `[MOTOR]` ni `base`.
3. **Proyectos: datos.** Definir `Proyecto[]` en el frontmatter de `Proyectos.astro` y derivar el destacado (`proyectos[0]`) y los secundarios (`slice(1)`) del mismo arreglo. Verificar: `astro build` sin errores de tipos.
4. **Proyectos: destacado.** En la columna izquierda reemplazar el diagrama por `<img>` (`src`, `alt`, `width`/`height` reales leídos del PNG, `loading="lazy"`, `decoding="async"`, `object-fit` para no desbordar) con la etiqueta `small` mostrando el nombre del archivo. Quitar `placeholder-note` y `leer_caso`. Tag de la pestaña = `género · plataforma`. Cambiar `repo` por `Jugar` (`href` = `itchUrl`, enlace externo) renderizado solo si hay `itchUrl`; ocultar `.actions` si queda vacío. Verificar: la tarjeta no queda con huecos y conserva el hover/foco magenta.
5. **Proyectos: secundarios.** Reemplazar `.shot` por `<img>` con la misma altura de 180 px (`object-fit: cover`), quitar `leer_caso`, tag = `género · plataforma`, mostrar patrón y stack unidos con ` · `, `Jugar` condicional (Dead's Loot sí, La Aventura Unal no). Verificar: las 3 tarjetas muestran su imagen a 390 px sin overflow.
6. **Laboratorio.** Definir `Experimento[]` con los 3 experimentos, renderizar las tarjetas `queued` según el flag y eliminar el bloque `queued` fijo, el `ver_preview` y el "último commit". Verificar: 3 tarjetas, las dos últimas con borde punteado y chip `en cola`.
7. **Contacto.** Cambiar email (texto + `mailto:`), valores y URLs de github y linkedin; añadir `target="_blank" rel="noopener noreferrer"` a los enlaces externos. Dejar la fila de CV intacta. Verificar: los 3 enlaces resuelven (el de CV sigue como placeholder).
8. **Cierre.** Buscar corchetes residuales en `src/` (`grep "\["`), confirmar que solo quedan los de CV, y ejecutar `astro build`.

## Criterios de aceptación

- [ ] `astro build` termina sin errores ni warnings de TypeScript/Astro.
- [ ] La marca del navbar muestra `~/juan-valencia_`, el footer `© 2026 Juan Valencia` y el `<title>` por defecto `~/juan-valencia — Portfolio`.
- [ ] El panel del hero lista `stack` con los 8 ítems y `motores: ["Unity", "Godot"]`, no contiene `base`, `[LENGUAJE]` ni `[MOTOR]`, y el gutter llega a 11 con `Ln 11, Col 3`.
- [ ] La sección Proyectos muestra exactamente 3 tarjetas: Berracos Espantos (destacada), La Aventura Unal y Dead's Loot, cada una con su `resumen`, `patrón`, `stack` y `rol` (el destacado) o `patrón`/`stack` (secundarios) del modelo de datos.
- [ ] Cada tarjeta de proyecto muestra su PNG con `alt` descriptivo; ninguna imagen provoca scroll horizontal a 390 px ni 1440 px.
- [ ] Las pestañas muestran `Videojuego · PC` (01 y 03) y `Videojuego · Android` (02); no aparecen `[GÉNERO · PLATAFORMA]`, `[Reemplazar por captura…]`, `leer_caso` ni `repo` en la sección Proyectos.
- [ ] Berracos Espantos y Dead's Loot muestran botón `Jugar` con sus `href` de itch.io (nueva pestaña, `rel="noopener noreferrer"`); La Aventura Unal no muestra botón `Jugar`.
- [ ] Laboratorio muestra 3 tarjetas: Pulse Arkade (`en construcción`, `stack: Next.js`) y dos Por Definir (`en cola`, borde punteado, `stack: Por Definir`).
- [ ] Laboratorio no contiene `ver_preview`, "último commit", la tarjeta `prototipo` ni `mkdir próximo_experimento`.
- [ ] El botón de contacto muestra `jcval.dev@gmail.com` y su `href` es `mailto:jcval.dev@gmail.com`.
- [ ] Los enlaces github y linkedin muestran `github/GiBsyGG` y `linkedin/juan-valencia`, apuntan a las URLs reales y abren en pestaña nueva con `rel="noopener noreferrer"`.
- [ ] El único placeholder entre corchetes que queda en `src/` es `[URL-CV]` (navbar, panel móvil y contacto).
- [ ] Hover/foco magenta, reveal, riel de scroll y menú móvil de la SPEC 01 siguen funcionando.
- [ ] No se añadieron dependencias nuevas a `package.json`.

## Decisiones tomadas y descartadas

- **Sí:** `personal-data.md` como única fuente. No se inventa copy, géneros ni plataformas; lo faltante se elimina o queda para otra spec.
- **Sí:** datos en el frontmatter de cada componente. Mantiene la convención de la SPEC 01 y evita crear una capa de datos para 3 proyectos.
- **No:** content collections o archivo `src/data/*.ts`. Es una refactorización con valor cuando existan las páginas de caso (spec futura).
- **Sí:** reemplazar el diagrama y el recuadro de captura por la imagen del proyecto. El diagrama genérico no aporta contenido real.
- **No:** conservar imagen + diagrama. Añade ruido sin información real.
- **Sí:** `<img>` simple apuntando a `public/images/projects/`. Las imágenes ya están ahí; `astro:assets` queda como optimización posterior.
- **Sí:** ocultar `leer_caso`. Su destino es otra spec; evitar enlaces muertos.
- **Sí:** `repo` pasa a `Jugar` y es condicional a `itchUrl` (campo "link itch.io" del archivo). Pedido explícito: sin enlace a itch.io no hay botón.
- **Sí:** tag `género · plataforma` con los campos `Género` y `Plataforma` del archivo, unidos con ` · `.
- **Sí:** el CV no se toca. Pedido explícito: pertenece a otra spec.
- **Sí:** `stack` y `motores` en dos entradas del panel (opción recomendada), con `stack` partido en líneas para no desbordar el ancho. `base` se elimina por pedido explícito.
- **Sí:** mantener 3 tarjetas en Laboratorio: Pulse Arkade más dos `queued` "Por Definir" (Experimento 2 y la tarjeta queued previa). Pedido explícito del usuario.
- **Sí:** handles cortos `GiBsyGG` y `juan-valencia`; la URL completa va en el `href`.
- **Sí:** `target="_blank" rel="noopener noreferrer"` en enlaces externos de contacto. Evita sacar al usuario del portfolio y cierra el acceso a `window.opener`.
- **Sí:** corregir `reutilzable` → `reutilizable`. Errata evidente, no cambia el contenido.
- **Sí:** quitar "último commit" de todas las tarjetas. Pedido explícito: no hay forma de actualizarlo automáticamente por ahora.

## Riesgos identificados

| Riesgo | Mitigación |
| --- | --- |
| Imágenes con proporciones distintas rompen la altura de las tarjetas | `object-fit: cover` con altura fija (180 px) en secundarios y `max-width: 100%` + `height: auto`/`aspect-ratio` en el destacado; revisar las 3 a 390 y 1440 px. |
| Peso de los PNG afecta la carga | `loading="lazy"` y `decoding="async"`; si pesan demasiado, mover a `astro:assets` en una spec de optimización. |
| La línea `stack` del hero desborda el panel | Partir el arreglo en 2 líneas dentro del `pre`; `overflow-x: auto` del editor como red de seguridad. |
| Sin botones, las tarjetas secundarias quedan con espacio vacío inferior | Quitar el `margin-top: auto` huérfano y verificar visualmente el equilibrio del grid. |
| Texto técnico largo en `resumen` hace la tarjeta destacada muy alta | Ya usa `flex-wrap`; revisar a 390 px y recortar copy solo si el usuario lo pide. |

## Qué **no** está en esta spec

- CV (archivo y enlaces).
- Páginas de caso de estudio y botón `leer_caso`.
- URL de itch.io de La Aventura Unal (el botón `Jugar` aparece al añadir `itchUrl`).
- Preview de experimentos y datos automáticos de commits.
- Envío real del formulario de contacto.
- Optimización de imágenes, SEO, i18n, analítica.

Cada uno, si se aborda, va en su propia spec.
