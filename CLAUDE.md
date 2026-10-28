# Portfolio personal — Juan Valencia

Sitio estático Astro 7, una sola página, contenido en español. Ver `AGENTS.md` para comandos del dev server (`astro dev --background`) y enlaces a docs de Astro.

## Comandos

- `npm run dev` / `npm run build` / `npm run preview`
- Node >= 22.23.4

## Estructura

- `src/pages/index.astro` — compone Navbar, Hero, Proyectos, Laboratorio, Contacto, Footer
- `src/layouts/Layout.astro` — head, meta, fuentes (Inter, JetBrains Mono), `<html lang="es">`
- `src/components/*.astro` — una sección por componente, datos en el frontmatter (arrays tipados)
- `src/styles/global.css` — tokens en `:root` (`--bg`, `--fg-*`, `--accent`, `--border`, `--font-*`); tema oscuro, acento magenta
- `public/images/projects/` — capturas de proyectos
- `specs/` — specs del MVP y contenido real; `references/personal-data.md` — datos personales

## Convenciones

- Despliegue en GitHub Pages bajo `base: '/JuanValDev/'`. Siempre construir rutas de assets con `import.meta.env.BASE_URL` (ver `Layout.astro`), nunca rutas absolutas `/...`.
- Textos de UI y contenido en español.
- Indentación con tabs en `.astro`.
- Colores y fuentes vía variables CSS de `global.css`; estilos específicos en `<style>` scoped del componente.
- Navegación por anclas (`#proyectos`, `#laboratorio`, `#contacto`); `Navbar` usa `data-section`.
- Sin dependencias extra: solo `astro`.

## Tooling

- Plugin Impeccable habilitado (`.claude/settings.json`); config en `.impeccable/`.
- Rama de trabajo `develop`, principal `main`.
