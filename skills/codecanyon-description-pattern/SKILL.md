---
name: codecanyon-description-pattern
description: Patrón canónico de descripción HTML para listings de CodeCanyon. Define las 12 secciones obligatorias, los placeholders genéricos para cualquier producto (Perfex, CI3, Node, Laravel, WP), reglas sobre imágenes (capturas reales NO IA generativa), y workflow de generación. Basado en demo.html (MailTrixy aprobado de Media City) + descripcion-aprobado-mio.html (TrackChat aprobado de ticempresarial). El TEMPLATE.html crudo está en este mismo skill. Activa cuando un agente vaya a generar descripción para CodeCanyon o el usuario corra /preparar-venta.
---

# Patrón canónico — Descripción HTML para CodeCanyon

> Define la estructura de descripción que aprueba Envato. Independiente del stack.
> Aplica a módulos Perfex, productos CI3, Node SaaS, Laravel SaaS, plugins WordPress.

## Filosofía

CodeCanyon recibe miles de submissions al mes. El reviewer escanea cada listing en
20-40 segundos antes de decidir. **Una descripción bien estructurada es la
diferencia entre soft-reject y aprobación.**

Las 12 secciones de este patrón fueron extraídas empíricamente de:
- **demo.html** (MailTrixy v1.3 por Media City — aprobado activo en CodeCanyon)
- **descripcion-aprobado-mio.html** (TrackChat por ticempresarial — aprobado activo)
- **descripcion-mio-crudo.html** (HTML crudo que el author sube literalmente)

## Archivo principal

El template HTML crudo está en `TEMPLATE.html` (mismo directorio que este SKILL.md).

**Cómo se usa**:
1. El agente lee `TEMPLATE.html`
2. Reemplaza los 80+ placeholders `[PLACEHOLDER]` con info real del producto
3. Entrega `descripcion-codecanyon.html` listo para copiar/pegar en el formulario de
   CodeCanyon

## Las 12 secciones del patrón (orden obligatorio)

| # | Sección | Obligatoria | Notas |
|---|---------|-------------|-------|
| 1 | Hello + 2 párrafos pitch | ✅ | Saludo amable + qué es + para qué |
| 2 | Authorization for Demo (admin + user creds) | ✅ | **ARRIBA**, no al final. Reviewer prueba en 5s |
| 3 | Who is it For? (5-6 audiencias) | ✅ | Tipos de comprador (Freelancers, SMB, Support Teams, etc.) |
| 4 | Use Cases (5-7 ítems) | ✅ | Lista corta de qué hace el producto |
| 5 | Galería de imágenes 1 (10-20 screenshots) | ✅ | Linkeadas al demo, alt único secuencial |
| 6 | Key Features (H3 + 4-15 subsecciones H4) | ✅ | LA SECCIÓN MÁS GRANDE. Cada subsec con bullets de strong+texto |
| 7 | Technology Stack (lista) | ✅ | Lo que usa: backend, frontend, DB, AI, channels, payment |
| 8 | Galería de imágenes 2 (3-5 imágenes extras) | ⚠️ Opcional | Refuerzo visual entre tech stack y notes |
| 9 | Notes legales (2 notas) | ✅ | Media license + dependencias 3rd party |
| 10 | 5 stars CTA (imagen) | ⚠️ Opcional | Link a downloads para que el comprador rate |
| 11 | Changelogs (2-3 versiones recientes) | ✅ | H3 + H6 por versión + `<pre>` con cambios |
| 12 | Follow Us (3-5 social links) | ✅ | Envato + Facebook + Twitter + opcionalmente más |

## Reglas duras (NO violar)

### Estructura HTML
1. ⛔ NO incluir `<html>`, `<head>`, `<body>` — solo el body content
2. ⛔ NO incluir `<style>` ni `<link rel="stylesheet">` — CodeCanyon agrega sus clases
3. ⛔ NO incluir `id` en los headings — CodeCanyon los genera de `item-description__<slug>`
4. ⛔ NO usar `<h1>` — el título del item lo pone CodeCanyon arriba automáticamente
5. ✅ Usar `<h3>` para secciones top-level, `<h4>` para subsecciones
6. ✅ Usar `<h6>` solo para fechas de release en Changelogs
7. ✅ Separadores entre secciones: `<br><br>` (NO `<hr>` — visualmente queda raro en CodeCanyon)

### Listas
1. ✅ Cada item de feature inicia con `<strong>Título:</strong> descripción.`
2. ✅ Punto final obligatorio en cada `<li>` de feature
3. ⛔ NO usar listas anidadas — confunden al reviewer

### Links
1. ✅ Links al demo siempre con `rel="nofollow"` (Envato regla SEO)
2. ✅ Email creds como link `<a href="mailto:...">email</a>`
3. ⛔ NO usar URLs con query params específicos a tu cuenta (los que ves en demo.html con `client_id=...` los agrega Envato al renderizar, NO los pongas tú)

### Imágenes (CRÍTICO)
1. ✅ Cada `<img>` con `alt` único secuencial: `<Product Name> - 1`, `<Product Name> - 2`, etc.
2. ✅ Imágenes envueltas en `<a>` que linkea al demo (para que el click vaya al demo)
3. ⛔ NO imágenes generadas por IA en la descripción (Envato rechaza)
4. ✅ Mínimo 10 capturas reales del producto, ideal 15-20
5. ✅ Hostear en: Cloudinary (recomendado, plan gratis) / tu propio servidor / Envato CDN
6. ⛔ NO usar imgur (suele banear) ni Google Drive (links cambian)

### Idioma
1. ✅ Toda la descripción en **inglés profesional** (CodeCanyon es global)
2. ⛔ NO mezclar idiomas (no español + inglés en el mismo texto)
3. ✅ Tono: profesional, directo, vendedor. Cero broma.

### Tono y longitud
1. ✅ Pitch inicial: 2 párrafos de 3-5 oraciones cada uno
2. ✅ Use Cases: 5-7 bullets cortos
3. ✅ Features: cada bullet con explicación de 1 oración (no solo el título)
4. ✅ Longitud total: 800-3000 palabras según complejidad del producto
5. ⛔ NO copy-paste de la documentación técnica — la descripción es de venta, no de manual

## Placeholders del template (80+)

Categorizados por sección. El agente debe llenar TODOS antes de entregar.

### Globales (usar en todas las secciones)
- `[PRODUCT_NAME]` — nombre comercial
- `[PRODUCT_TAGLINE]` — pitch de 1 frase
- `[STACK]` — Laravel / Node / CI3 / Perfex / WordPress
- `[VERSION]` — versión actual
- `[RELEASE_DATE]` — fecha release

### Sección 1 — Hello + pitch
- `[SECONDARY_VALUE_PROPOSITION]` — segunda dimensión del valor
- `[USE_CASE_1]`, `[USE_CASE_2]`, `[USE_CASE_3]` — 3 ejemplos de uso
- `[KEY_DIFFERENTIATOR]` — qué hace único al producto

### Sección 2 — Demo
- `[DEMO_URL]` — URL del demo admin
- `[DEMO_EMAIL]`, `[DEMO_PASSWORD]` — creds admin
- `[DEMO_USER_URL]` — URL portal usuario (si aplica)
- `[DEMO_USER_EMAIL]`, `[DEMO_USER_PASSWORD]` — creds usuario

### Sección 3 — Audiencias
- `[AUDIENCE_1_TITLE]` a `[AUDIENCE_6_TITLE]` + sus descripciones

### Sección 4 — Use Cases
- `[USE_CASE_1]` a `[USE_CASE_7]`

### Sección 5 — Galería 1
- `[IMAGE_URL_1]` a `[IMAGE_URL_15]` — URLs públicas de los screenshots

### Sección 6 — Features
- `[FEATURE_SECTION_N_TITLE]` (N=1..4) — títulos H4
- `[FEATURE_N_M_TITLE]` y `[FEATURE_N_M_DESCRIPTION]` — bullets
- `[SECURITY_N_TITLE]` y `[SECURITY_N_DESCRIPTION]` — security
- `[INTEGRATION_N_TITLE]` y `[INTEGRATION_N_DESCRIPTION]` — integraciones

### Sección 7 — Tech Stack
- `[BACKEND_STACK]`, `[FRONTEND_STACK]`, `[DATABASE_STACK]`
- `[AI_STACK_IF_APPLIES]`, `[EMAIL_STACK_IF_APPLIES]`
- `[CHANNELS_IF_APPLIES]`, `[PAYMENT_GATEWAYS_IF_APPLIES]`

### Sección 8 — Galería 2
- `[IMAGE_URL_16]` a `[IMAGE_URL_19]`

### Sección 9 — Notes
- `[THIRD_PARTY_DEPENDENCIES_NOTE_IF_ANY]` — dependencias 3rd party (OpenAI, Twilio, etc.)

### Sección 10 — 5 stars
- `[IMAGE_5_STARS_URL]` — URL imagen 5 estrellas (reusable cross-products)

### Sección 11 — Changelogs
- `[CHANGELOG_LINE_1..N]` — líneas de la versión actual
- `[PREVIOUS_VERSION]`, `[PREVIOUS_RELEASE_DATE]` — versión anterior
- `[PREVIOUS_CHANGELOG_LINE_1..N]` — líneas versión anterior

### Sección 12 — Follow Us
- `[SOCIAL_ENVATO_URL]` — tu perfil Envato (https://codecanyon.net/user/ticempresarial)
- `[SOCIAL_FACEBOOK_URL]`, `[SOCIAL_TWITTER_URL]`
- `[IMAGE_SOCIAL_ENVATO]`, `[IMAGE_SOCIAL_FACEBOOK]`, `[IMAGE_SOCIAL_TWITTER]` — iconos (reusables)

## Workflow de generación (3 fases)

### Fase A — Información del producto
El agente lee:
- `<modulo>.php` o `package.json` o `composer.json` → nombre, versión
- `README.md` → pitch, features, stack
- `ARQUITECTURA.md` (si existe) → audiencias, use cases, security, integrations
- `CHANGELOG.md` → changelogs

### Fase B — Screenshots reales
Invoca al agente `screenshot-capturer`:
- Levanta demo (local o público)
- chrome-devtools MCP → login admin
- Navega 15-20 URLs declaradas
- Captura fullPage 1920x1080
- Sube a Cloudinary (o hosting configurado)
- Retorna array de URLs públicas

### Fase C — Assets del listing (icon/preview/cover)
Invoca al skill `codecanyon-image-prompts`:
- Genera 3 prompts optimizados (uno por asset)
- Tú los pegas en Midjourney / DALL-E / Imagen
- Subes los PNG resultantes a `sales/listing-assets/`

## Diferencia screenshots descripción vs assets listing

⚠️ **No confundir**. Son DOS tipos distintos.

| Aspecto | Screenshots de la **descripción** (placeholders IMAGE_URL_N) | Assets del **listing** (icon/preview/cover) |
|---------|------------------------------------------------------------|---------------------------------------------|
| Generados con IA | ⛔ NO (Envato rechaza mockups) | ✅ SÍ (con Midjourney/DALL-E) |
| Cantidad | 15-20 | 3 fijos |
| Tamaño | 1920x1080 (recomendado) | 80x80 / 590x300 / 2340x1560 |
| Cómo se obtienen | chrome-devtools MCP capturando demo real | Prompts a herramientas IA generativas |
| Donde van | En el HTML de la descripción | Subidos como assets aparte en CodeCanyon |
| Skill responsable | Este (`codecanyon-description-pattern`) | `codecanyon-assets` + `codecanyon-image-prompts` |

## Anti-patterns

| Anti-pattern | Por qué falla |
|--------------|---------------|
| Authorization al final | Reviewer no se molesta en bajar — abandona |
| Sin screenshots (solo texto) | Envato considera "low effort listing" |
| Screenshots con `[DEMO_TEST]` o `Lorem Ipsum` visibles | Rechazo por unprofessional |
| Screenshots con mockups falsos generados por IA | Rechazo + ban si reincides |
| Features genéricas sin explicación | "Customer Management" sin nada más → reject |
| Sin Tech Stack | Reviewer no sabe qué requiere → reject |
| Sin Changelogs (en v1.0.0 OK pero declarar "1.0 — Initial release") | Listing parece abandonado |
| Sin Follow Us | Pierde oportunidad de cross-sell tus otros productos |
| Idiomas mezclados | Rechazo automático |
| Múltiples `<h1>` | CodeCanyon ya tiene h1 → conflicto SEO |
| Inline styles (`<p style="color:red">`) | CodeCanyon los strippea → diseño roto |

## Checklist pre-submit

```
[ ] 12 secciones presentes en orden
[ ] Authorization arriba (no al final)
[ ] 5-6 audiencias en Who is it For?
[ ] 5-7 use cases
[ ] 15+ screenshots reales (no IA, no [DEMO_TEST])
[ ] Key Features con al menos 4 H4 + 4-6 bullets cada uno
[ ] Tech Stack listado
[ ] 2 notes legales presentes
[ ] Changelogs con al menos versión actual
[ ] Follow Us con 3+ links
[ ] Inglés profesional (cero typos)
[ ] Cero IDs en headings (los pone CodeCanyon)
[ ] Cero inline styles
[ ] Cero <h1>
[ ] Cero query params client_id en los demos
[ ] Cero imgur / google drive (usar Cloudinary)
```

## Cuándo invocar este skill

- En el agente `codecanyon-release` cuando va a empaquetar venta
- Cuando el usuario corre `/preparar-venta`
- Cuando el usuario pregunte "cómo escribo la descripción para CodeCanyon"

## Versionado

- v1.0 (2026-05-28): inicial. Basado en MailTrixy v1.3 aprobado + TrackChat aprobado + sesión de análisis comparativo.
