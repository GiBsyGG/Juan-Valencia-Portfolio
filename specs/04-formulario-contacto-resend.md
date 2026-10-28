# SPEC 04 — Formulario de contacto con Resend

> **Estado:** Implementado
> **Depende de:** SPEC 01, SPEC 02
> **Fecha:** 2026-10-28
> **Objetivo:** Hacer que el formulario de `Contacto.astro` envíe el mensaje por correo a `jcval.dev@gmail.com` mediante un Cloudflare Worker que llama a la API de Resend, manteniendo el sitio estático en GitHub Pages.

## Por qué existe esta spec

La SPEC 01 dejó el formulario solo como UI y la SPEC 02 excluyó el "envío real" explícitamente. GitHub Pages solo sirve archivos estáticos, y la API key de Resend no puede viajar en el HTML del cliente (cualquiera podría usarla). Por eso hace falta un backend mínimo: un Worker de Cloudflare (plan gratis) que guarda la key como secreto, valida la petición y llama a Resend. El sitio no cambia de hosting.

## Alcance

**Dentro:**

- Nuevo directorio `worker/` con el Worker de Cloudflare (`worker/src/index.js`, `worker/wrangler.toml`). Sin `package.json` propio: se usa `npx wrangler`.
- Endpoint `POST` del Worker que valida el cuerpo y envía el correo vía `POST https://api.resend.com/emails`.
- Remitente `Portfolio <onboarding@resend.dev>`, destino `jcval.dev@gmail.com`, `reply_to` = correo del visitante.
- Protección anti-spam: honeypot, CORS restringido al origen del sitio y validación de longitudes y formato en el Worker.
- Cliente: `<form>` de `Contacto.astro` pasa a enviar con `fetch`, con `name` en los campos, campo honeypot oculto, estados `Enviando…` / éxito / error inline.
- Guía de configuración paso a paso (Resend, Cloudflare, secretos, despliegue, prueba) en `worker/README.md`.
- Añadir `worker/` a `.gitignore`: el Worker, su README y `.dev.vars` quedan solo en local y no se versionan.
- Incluir en la guía las medidas de seguridad de cuentas y plan gratuito (ver "Seguridad y costos").
- Actualizar `CLAUDE.md` con la carpeta `worker/` y la convención del endpoint.

**Fuera de alcance (specs futuras):**

- Dominio propio y remitente `@tudominio` (hoy solo `onboarding@resend.dev`).
- Cloudflare Turnstile u otro captcha.
- Rate limiting por IP (Workers KV / Rate Limiting API).
- Correo de confirmación al visitante.
- Guardar mensajes (base de datos / KV).
- Plantilla HTML del correo (se envía texto plano).
- CI para desplegar el Worker (el despliegue es manual con `wrangler deploy`).
- Cambios de diseño del formulario.

## Modelo de datos

Sin persistencia. Dos contratos nuevos.

**Petición del cliente al Worker** (`POST`, `Content-Type: application/json`):

```ts
interface MensajeContacto {
  nombre: string;     // 1–100 caracteres, tras trim
  correo: string;     // formato de email, máx. 254
  mensaje: string;    // 1–5000 caracteres, tras trim
  empresa: string;    // honeypot: debe llegar vacío
}
```

**Respuesta del Worker:**

```ts
// 200
{ ok: true }
// 400 validación, 403 origen no permitido, 405 método, 502 fallo de Resend
{ ok: false, error: string }   // mensaje corto en español, apto para mostrar
```

Si `empresa` viene con texto, el Worker responde `200 { ok: true }` sin enviar nada (el bot no aprende que fue detectado).

**Configuración del Worker** (`worker/wrangler.toml` y secretos):

| Nombre | Tipo | Valor |
| --- | --- | --- |
| `name` | `wrangler.toml` | `portfolio-contacto` |
| `ALLOWED_ORIGINS` | `[vars]` | `https://gibsygg.github.io` |
| `MAIL_TO` | `[vars]` | `jcval.dev@gmail.com` |
| `MAIL_FROM` | `[vars]` | `Portfolio <onboarding@resend.dev>` |
| `RESEND_API_KEY` | secreto (`wrangler secret put`) | `re_…` (nunca en el repo) |

Para desarrollo local, `worker/.dev.vars` (ignorado por git) contiene `RESEND_API_KEY=…` y `ALLOWED_ORIGINS=http://localhost:4321,https://gibsygg.github.io`.

**Cliente (`Contacto.astro`):** constante en el frontmatter `const endpoint = 'https://portfolio-contacto.<subdominio>.workers.dev'`. La URL es pública, así que no usa variables de entorno ni toca el workflow de GitHub Actions.

## Plan de implementación

1. **Cuentas y guía.** Crear `worker/README.md` con la guía de configuración (ver sección "Guía de configuración" abajo). Verificar: el README lista cuentas, comandos y pruebas sin pasos faltantes.
2. **Worker mínimo.** Crear `worker/wrangler.toml` y `worker/src/index.js` con el handler `fetch` que responde `405` a todo salvo `POST`/`OPTIONS`, aplica CORS y devuelve `{ ok: true }` sin llamar a Resend. Verificar que `worker/` ya está en `.gitignore` (`git check-ignore worker/src/index.js`). Verificar: `npx wrangler dev` y `curl -X POST` responde 200 con origen permitido y 403 con otro origen.
3. **Validación + Resend.** En `index.js` añadir parseo JSON, validación de `MensajeContacto` (longitudes, regex de correo, honeypot) y la llamada `fetch` a `https://api.resend.com/emails` con `Authorization: Bearer ${env.RESEND_API_KEY}`, `from`, `to: [env.MAIL_TO]`, `reply_to: [correo]`, `subject: "Portfolio: mensaje de <nombre>"`, `text`. Verificar con `wrangler dev` y `.dev.vars`: llega un correo a `jcval.dev@gmail.com`; payload inválido da 400; honeypot lleno da 200 sin correo.
4. **Despliegue del Worker.** `npx wrangler secret put RESEND_API_KEY` y `npx wrangler deploy` desde `worker/`. Anotar la URL `workers.dev`. Verificar: `curl` contra la URL pública con `Origin: https://gibsygg.github.io` entrega un correo real.
5. **Cliente.** En `Contacto.astro`: añadir `name` a los 3 campos y un `input` honeypot (`name="empresa"`, `tabindex="-1"`, `autocomplete="off"`, oculto visualmente y con `aria-hidden`), `required` y `maxlength` en los campos, cambiar el `button` a `type="submit"`, y un `<p class="status" role="status" aria-live="polite">` bajo el botón. Verificar: estructura renderiza sin cambios visuales a 390 px y 1440 px.
6. **Envío desde el cliente.** Script en `Contacto.astro` que intercepta `submit`, bloquea el botón con texto `Enviando…`, hace `fetch(endpoint, { method: 'POST', … })` y muestra éxito (`Mensaje enviado. Te respondo pronto.`, formulario se limpia) o error (mensaje del Worker o `No se pudo enviar. Escríbeme a jcval.dev@gmail.com.`, campos conservados). Estilos del `.status` con variables de `global.css`, tabs de indentación. Verificar con `astro dev` (origen `localhost:4321` permitido en `.dev.vars`).
7. **Cierre.** Actualizar `CLAUDE.md` (carpeta `worker/`, endpoint, secretos), ejecutar `astro build` y probar el formulario contra el Worker desplegado desde `astro preview`.

## Guía de configuración (contenido de `worker/README.md`)

**Lo que necesitas antes de implementar:**

1. **Cuenta Resend** registrada con `jcval.dev@gmail.com`. Sin dominio verificado, Resend solo permite enviar a ese correo con el remitente `onboarding@resend.dev`.
2. **API key de Resend:** panel Resend → API Keys → Create (permiso "Sending access"). Se muestra una sola vez; guárdala.
3. **Cuenta Cloudflare** (gratis, sin tarjeta). Al desplegar por primera vez, Cloudflare pide elegir un subdominio `workers.dev`.
4. **Node ≥ 22** (ya requerido por el proyecto). `npx wrangler login` abre el navegador para autorizar.
5. **Origen exacto del sitio:** `https://gibsygg.github.io` (solo origen, sin `/JuanValDev/`).

**Seguridad y costos (sección obligatoria de `worker/README.md`):**

1. Activar 2FA en Cloudflare y en Resend, con contraseñas únicas.
2. No registrar tarjeta ni subir de plan en ninguno de los dos. En los planes gratis, al llegar al tope el servicio rechaza peticiones y no cobra excedentes (límites aproximados: Workers 100.000 peticiones/día; Resend 100 correos/día y 3.000/mes; confirmar en sus páginas de precios).
3. Crear la API key de Resend con permiso "Sending access" únicamente.
4. La key solo vive como secreto de Cloudflare y en `worker/.dev.vars`; nunca en el repo ni en el cliente.
5. Si se filtra la key: borrarla en Resend, crear otra y ejecutar de nuevo `wrangler secret put RESEND_API_KEY`.
6. Si llega spam: desactivar o borrar el Worker desde el panel de Cloudflare; el formulario deja de funcionar y el `mailto:` sigue disponible.
7. Si algún día se agrega tarjeta, configurar alertas de gasto antes.

**Comandos (desde `worker/`):**

```bash
npx wrangler login
npx wrangler dev                       # local, lee .dev.vars
npx wrangler secret put RESEND_API_KEY # pega la key cuando lo pida
npx wrangler deploy                    # imprime la URL workers.dev
```

**Prueba rápida del Worker desplegado:**

```bash
curl -X POST https://portfolio-contacto.<subdominio>.workers.dev \
  -H "Origin: https://gibsygg.github.io" -H "Content-Type: application/json" \
  -d '{"nombre":"Prueba","correo":"a@b.co","mensaje":"Hola","empresa":""}'
```

## Criterios de aceptación

- [ ] `astro build` termina sin errores ni warnings de TypeScript/Astro.
- [ ] `package.json` y `package-lock.json` de la raíz no cambian; no se añadió ninguna dependencia.
- [ ] `RESEND_API_KEY` no aparece en ningún archivo versionado (`git grep "re_"` sin coincidencias de keys reales) y `worker/` está en `.gitignore` (`git check-ignore worker/.dev.vars` lo confirma).
- [ ] `worker/README.md` incluye la sección "Seguridad y costos" con: 2FA en Cloudflare y Resend, no registrar tarjeta ni subir de plan, API key con permiso "Sending access" y pasos para rotar la key.
- [ ] Las cuentas de Cloudflare y Resend están en plan gratuito, sin tarjeta registrada y con 2FA activo (verificación manual en sus paneles).
- [ ] `POST` al Worker con origen `https://gibsygg.github.io` y payload válido responde `200 { "ok": true }` y llega un correo a `jcval.dev@gmail.com` con asunto `Portfolio: mensaje de <nombre>`.
- [ ] Al pulsar "Responder" en ese correo, el destinatario es el `correo` del visitante (`reply_to`).
- [ ] `POST` con `Origin` distinto (ej. `https://evil.example`) responde `403` y no envía correo.
- [ ] `GET` al Worker responde `405`.
- [ ] Payload con `nombre` vacío, `correo` sin formato válido, `mensaje` > 5000 caracteres o JSON inválido responde `400 { ok: false, error }` sin llamar a Resend.
- [ ] Payload con `empresa` no vacío responde `200 { ok: true }` y no llega correo.
- [ ] En el sitio, al enviar, el botón queda deshabilitado con texto `Enviando…` hasta recibir respuesta.
- [ ] Con envío exitoso aparece `Mensaje enviado. Te respondo pronto.` en un elemento `role="status"`, los 3 campos se vacían y el botón vuelve a `Enviar mensaje`.
- [ ] Con el Worker caído o error 4xx/5xx aparece un mensaje de error, los campos conservan lo escrito y el botón se reactiva.
- [ ] El campo honeypot no es visible ni enfocable con teclado.
- [ ] La página no recarga ni navega al enviar.
- [ ] Los campos siguen con `label` asociado por `for`/`id` y el formulario no genera scroll horizontal a 390 px ni 1440 px.
- [ ] `worker/README.md` existe y contiene los comandos `wrangler login`, `wrangler secret put RESEND_API_KEY` y `wrangler deploy`.
- [ ] `CLAUDE.md` menciona la carpeta `worker/`.

## Decisiones tomadas y descartadas

- **Sí:** Cloudflare Worker como backend. Plan gratis sin tarjeta, no obliga a mover el sitio de GitHub Pages y mantiene la key de Resend fuera del cliente.
- **No:** Vercel/Netlify functions. Pedido explícito del usuario de evitar Vercel salvo que no hubiera alternativa; sí la hay.
- **No:** quitar el formulario (solo `mailto:`). El usuario quiere usar Resend; el `mailto:` se conserva como botón y como fallback en el mensaje de error.
- **No:** migrar Astro a SSR con adapter. Cambia despliegue, `base` y workflow para resolver un endpoint de 40 líneas.
- **Sí:** `onboarding@resend.dev` como remitente. El usuario no tiene dominio; funciona solo si la cuenta de Resend está registrada con `jcval.dev@gmail.com`. El remitente es variable (`MAIL_FROM`), así que migrar a dominio propio no toca código.
- **Sí:** `reply_to` = correo del visitante. Permite responder directo desde Gmail sin que el remitente técnico estorbe.
- **Sí:** honeypot + CORS + validación, sin Turnstile. Elección del usuario; suficiente para un portfolio y sin scripts ni claves extra.
- **Sí:** honeypot responde `200`. El bot no recibe señal para adaptarse.
- **Sí:** CORS por lista de orígenes en variable. Permite `localhost:4321` solo en desarrollo vía `.dev.vars`; el CORS no protege contra `curl`, solo contra otros sitios web (el riesgo restante se acepta, ver riesgos).
- **Sí:** mensaje de estado inline con `role="status"`. Elección del usuario; accesible y sin dependencias.
- **Sí:** URL del Worker como constante en el frontmatter. No es secreta, evita variables de entorno en Actions y es un único punto a cambiar.
- **Sí:** `worker/` sin `package.json`, con `npx wrangler`. Respeta "solo `astro`" como dependencia del sitio y evita un segundo árbol de dependencias.
- **Sí:** `worker/` ignorado por git. Pedido explícito del usuario; el código del Worker, su README y los secretos locales no se suben al repo. Costo: sin respaldo en git, si se pierde la carpeta hay que reescribir el Worker (es corto y la spec lo describe).
- **Sí:** quedarse en planes gratuitos, sin tarjeta, con 2FA. Al llegar al tope los servicios rechazan peticiones en vez de cobrar, así que el riesgo económico es nulo.
- **Sí:** correo en texto plano. Basta para leer mensajes; plantilla HTML es refinamiento posterior.
- **No:** `mailto:` como acción del formulario. Ya descartado en la SPEC 01.

## Riesgos identificados

| Riesgo | Mitigación |
| --- | --- |
| Alguien llama al Worker con `curl` ignorando CORS y llena la bandeja o agota la cuota de Resend (100/día, 3000/mes en plan gratis) | Validación estricta, honeypot y límite de tamaño. Si ocurre, spec futura con Turnstile o Rate Limiting; mientras tanto se puede rotar la key o desactivar el Worker. |
| La cuenta Resend no está registrada con `jcval.dev@gmail.com` y la API rechaza el envío (403) | Paso 1 de la guía lo exige; el Worker devuelve 502 con mensaje genérico y el cliente muestra el fallback con el correo. |
| Los correos de `onboarding@resend.dev` caen en spam | Aceptado mientras no haya dominio propio; revisar spam en la prueba y marcar "no es spam". Solución definitiva: dominio verificado (spec futura). |
| La URL `workers.dev` cambia o el Worker se renombra y el cliente queda apuntando a un endpoint viejo | La constante `endpoint` es el único punto; documentado en `worker/README.md` y `CLAUDE.md`. |
| Un cliente sin JS no puede enviar | Se acepta; el enlace `mailto:` sigue disponible en la misma sección. |
| Se filtra la API key por commitearla | `worker/` ignorado, key solo como secreto de Cloudflare; criterio de aceptación con `git grep`. |
| Se pierde la carpeta `worker/` al no estar versionada | El código está descrito en esta spec y es corto; el Worker desplegado sigue vivo en Cloudflare y su código se puede ver desde el panel. |
| Robo de cuenta de Cloudflare o Resend | 2FA y contraseñas únicas (sección "Seguridad y costos"). |

## Qué **no** está en esta spec

- Dominio propio y remitente personalizado.
- Turnstile / captcha.
- Rate limiting por IP.
- Correo de confirmación al visitante.
- Guardado de mensajes.
- Plantilla HTML del correo.
- CI para desplegar el Worker.
- Cambios de diseño del formulario.

Cada uno, si se aborda, va en su propia spec.
