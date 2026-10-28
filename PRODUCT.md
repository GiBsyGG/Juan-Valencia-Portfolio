# Product

<!-- impeccable:product-schema 1 -->

## Platform

web

## Users

Reclutadores y empresas que contratan desarrolladores de software; estudios de videojuegos que buscan perfiles de gameplay/arquitectura (Unity, Godot); clientes freelance con proyectos por encargo. Llegan a evaluar rápido si Juan Valencia domina tanto web como videojuegos y cómo piensa la arquitectura.

## Product Purpose

Portfolio personal de una sola página de Juan Valencia, Desarrollador de Software e Ingeniero de Sistemas. Existe para conseguir empleo o proyectos: mostrar trabajo real (videojuegos), experimentos en curso y un canal de contacto directo. Éxito: el visitante entiende el perfil, abre un proyecto (itch.io) y contacta o descarga el CV.

## Positioning

Puente entre desarrollo web y videojuegos: stack de lenguajes (C#, Spring Boot, React, Angular, Flutter, Python, Express.js, Next.js) y motores (Unity, Godot). Los proyectos se explican por el problema técnico y el patrón que lo resuelve (MVC, HSM, Object Pooling, ScriptableObjects, Random Walk + BSP).

## Operating Context

Sitio estático Astro desplegado en GitHub Pages (`base: '/Juan-Valencia-Portfolio/'`). Una página con secciones: hero, proyectos, laboratorio, contacto. Rama de trabajo `develop`, principal `main`. Datos fuente en `references/personal-data.md`; specs en `specs/`.

## Capabilities and Constraints

- Contenido y UI solo en español; sin i18n ni modo claro por ahora.
- Sin dependencias extra: solo `astro`, JS vanilla.
- Pendientes: `[URL-CV]` en `Navbar.astro`; formulario de contacto solo UI, sin envío real; "Experimento 2" sin definir (tarjeta en cola); ciudad/país eliminado del hero.
- "Último commit" de tarjetas de laboratorio eliminado hasta tener actualización automática.

## Brand Commitments

Nombre: Juan Valencia. Estética terminal/editor de código con acento magenta sobre fondo oscuro (ya implementada; decisiones visuales viven en el código, no aquí).

## Evidence on Hand

- Proyectos reales: Berracos Espantos (itch.io, PC), La Aventura Unal (Android, sin enlace público), Dead's Loot (itch.io, PC). Capturas en `public/images/projects/`.
- Laboratorio: Pulse Arkade (en construcción, Next.js; explora harness de IA con patrón orquestador-trabajadores).
- Contacto: jcval.dev@gmail.com, github.com/GiBsyGG, linkedin.com/in/juan-valencia-a13161234/.
- Ausentes (no fabricar): testimonios, clientes, métricas, fechas, CV publicado.

## Product Principles

1. Mostrar trabajo real y verificable; nunca inventar prueba social ni métricas.
2. Cada proyecto se presenta por problema técnico y patrón, no solo por resultado.
3. Presentar web y videojuegos como un solo perfil coherente.
4. Contacto y CV a un clic de distancia para reclutadores con poco tiempo.
5. Mantener el sitio simple, estático y sin dependencias.
