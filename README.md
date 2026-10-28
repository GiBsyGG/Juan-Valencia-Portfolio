# Juan Valencia — Portfolio

Portfolio personal de Juan Valencia, desarrollador de software e ingeniero de sistemas. Sitio estático de una sola página, en español.

- **Producción:** https://gibsygg.github.io/JuanValDev/

## Stack

- [Astro](https://astro.build) 7 (sitio estático, sin frameworks UI)
- CSS global + estilos scoped por componente
- Fuentes: Inter y JetBrains Mono (Google Fonts)
- Node >= 22.23.4

## Secciones

Orden en `src/pages/index.astro`:

1. **Hero**
2. **Proyectos** — Berracos Espantos, La Aventura Unal, Dead's Loot
3. **Laboratorio** — experimentos en construcción / en cola (p. ej. Pulse Arkade)
4. **Contacto**
5. **Footer**

## Estructura

```text
/
├── public/            favicon e imágenes (images/projects/)
├── specs/             especificaciones (MVP, contenido real)
├── references/        datos personales de referencia
├── src/
│   ├── components/    Navbar, Hero, Proyectos, Laboratorio, Contacto, Footer
│   ├── layouts/       Layout.astro (head, fuentes, meta)
│   ├── pages/         index.astro
│   └── styles/        global.css (tokens de color y tipografía)
└── astro.config.mjs   site + base para GitHub Pages
```

## Comandos

| Comando           | Acción                                   |
| :---------------- | :--------------------------------------- |
| `npm install`     | Instala dependencias                     |
| `npm run dev`     | Servidor local en `localhost:4321`       |
| `npm run build`   | Build de producción en `./dist/`         |
| `npm run preview` | Previsualiza el build                    |

## Despliegue

GitHub Pages. `astro.config.mjs` define `site` y `base` (`/Juan-Valencia-Portfolio/`). Rutas de imágenes y favicons usan `import.meta.env.BASE_URL` para funcionar bajo ese subpath.

## Pendiente

- Reemplazar el placeholder `[URL-CV]` del botón "Descargar CV" en `Navbar.astro`.
