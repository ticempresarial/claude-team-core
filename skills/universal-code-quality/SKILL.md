---
name: universal-code-quality
description: Patrón canónico de calidad de código UNIVERSAL — agnóstico de stack. Define las 15 dimensiones de calidad que TODO producto comercial debe cumplir antes de venderse (CodeCanyon, marketplace, cliente directo) independiente del stack (PHP/Perfex/CI3/Laravel, Node/Express/Next/Nest, React/Vue/Angular SPA, Python/Django/FastAPI, Go, Ruby, Java, etc.). Basado en análisis empírico de productos APROBADOS en CodeCanyon de stacks distintos - DupliGuard (Perfex módulo), TicketYA (CI3 standalone), Framecast (Next.js + Supabase), WhatsAppQR (JS + React + Express + SQLite). Activa cuando el agente codecanyon-qa o universal-code-auditor revise un producto cuyo stack NO encaja en los canonical patterns específicos (node-canonical-pattern, ci3-canonical-pattern, envato-canonical-pattern) o cuando el usuario pida una auditoría general de calidad.
---

# Patrón canónico — Calidad de código universal (stack-agnostic)

> Equivalente neutral de los `*-canonical-pattern` específicos. Define lo que
> es VERDAD para todo producto comercial vendible, sin atarse a Next.js,
> CodeIgniter, Laravel, NestJS o cualquier framework específico.

## Cuándo este skill aplica

Usa este skill cuando:
- El producto NO encaja en los stacks canónicos documentados (Perfex módulo / CI3 standalone / Next.js+Supabase)
- El producto es híbrido (Node + React SPA + Express + SQLite, por ejemplo)
- El usuario pide "auditar calidad de código" sin especificar estándares específicos
- Hay duda sobre qué rúbrica aplicar

Cuando el producto SÍ encaja en un canonical pattern específico, usa **además** ese skill — los específicos profundizan, este universal cubre el piso común.

## Filosofía

**Calidad de código es lo que permite que un comprador en CodeCanyon entienda,
modifique y mantenga tu producto SIN preguntarte por chat.** Independiente del stack.

Las 15 dimensiones de calidad universal son las que diferencian un producto que
pasa Envato de uno que se rechaza por "lazy code". No importa si es PHP o
TypeScript — un controlador con `console.log` en producción es señal de descuido
en cualquier lenguaje.

## Productos referencia (para calibrar criterios)

| Producto | Stack | Estado | LOC | Lección clave |
|----------|-------|--------|-----|---------------|
| DupliGuard v1.1.1 | Perfex módulo (PHP/CI3) | Aprobado CodeCanyon 2026-05 | ~3,500 | Comentarios selectivos, no Lorem |
| TicketYA v1.0.0 | CI3 standalone (PHP) | Aprobado-ready | ~5,000 | Wizard install + RBAC nativo |
| Framecast AI v1.0.0 | Next.js + Supabase + TS | Aprobado CodeCanyon | ~24,358 | RLS + multi-payment + i18n |
| WhatsAppQR v0.4.0 | JS + React + Express + SQLite | Pre-submit (validado interno) | ~6,500 | Argon2 + Pino + CSRF + Zod server |

## Las 15 dimensiones de calidad universal

### 1. Estructura y arquitectura
- ✅ El proyecto tiene **un solo punto de entrada claro** (index.php, server.js, main.ts, etc.)
- ✅ Hay **separación clara de capas** (controllers/models/views o routes/services/data o equivalente)
- ✅ Folders top-level tienen **propósito declarado** (en README, en código, o por convención obvia)
- ✅ NO hay **carpetas huérfanas** con archivos sueltos sin relación
- ✅ Folder estructure refleja el **dominio del producto**, no solo el framework

### 2. Naming consistency
- ✅ Los archivos siguen UN patrón consistente dentro de cada capa (kebab-case / camelCase / PascalCase)
- ✅ Las funciones/métodos siguen UN patrón (camelCase JS, snake_case PHP convention)
- ✅ Las clases siempre PascalCase
- ✅ Las constantes siempre UPPER_SNAKE_CASE
- ✅ Las tablas/columnas BD en snake_case
- ⛔ Cero archivos con sufijos `2`, `new`, `final`, `copy`, `v2` que evidencian no-cleanup
- ⛔ Cero variables `tmp`, `temp`, `data1`, `data2`, `aux`

### 3. Comentarios y documentación (lo más importante para vendibilidad)
- ✅ Cada **archivo de library/service exportable** tiene un docblock de 1-3 líneas explicando su rol
- ✅ Cada **función pública con contrato no-obvio** tiene docblock explicando: qué hace, side effects, edge cases
- ✅ Cada **función con lógica compleja** tiene comentarios `// por qué` (no `// qué`)
- ✅ Cada **clase pública** tiene docblock describiendo responsabilidad
- ⛔ Cero docblocks **redundantes con tipos** (en TS: `@param {string} name - the name` es ruido)
- ⛔ Cero comentarios **que repiten el código** (`// add 1 to count` para `count++`)
- ⛔ Cero `// TODO`, `// FIXME`, `// XXX`, `// HACK` sin resolver
- ⛔ Cero **banners ASCII art**
- ⛔ Cero **`@author Juan` en cada archivo** (info de license va en LICENSE.txt)
- 🎯 **Ratio objetivo**: 1-8% de líneas comentadas según tipo de archivo (controllers/views: 1-3%, libraries: 5-10%)

### 4. Dead code y limpieza pre-release
- ⛔ Cero archivos `.bak`, `.old`, `.orig`, `.original`, `~`
- ⛔ Cero archivos comentados completos (5+ líneas comentadas seguidas en producción)
- ⛔ Cero funciones declaradas y no usadas en código de producto
- ⛔ Cero imports no usados (lint debe estar limpio)
- ⛔ Cero archivos duplicados con sufijo numérico
- ⛔ Cero comentarios `// removed X`, `// migration: Y removed`

### 5. Logging y debugging residual
- ⛔ Cero `console.log` en JS/TS de producción (warn/error con propósito sí)
- ⛔ Cero `var_dump`, `print_r`, `dd()`, `dump()` en PHP de producción
- ⛔ Cero `print()` Python sueltos
- ⛔ Cero `fmt.Println("debug")` Go sueltos
- ⛔ Cero `debugger` statements
- ✅ Logging estructurado disponible (Pino/Winston/bunyan en Node; Monolog en PHP; logging stdlib en Python; pino o equivalente)
- ✅ **PII redaction** en logger config (passwords, apiKeys, tokens, emails enmascarados por default)
- ✅ Niveles de log configurables vía env (LOG_LEVEL)

### 6. Seguridad — validación de inputs
- ✅ Validación con **schema** en todos los endpoints públicos (Zod/Joi/express-validator JS; FormRequest Laravel; pydantic Python; etc.)
- ✅ Validación en **server-side** SIEMPRE (no confiar en client)
- ✅ Errores de validación devuelven mensaje claro con código consistente (`400 + { error: 'validation', issues: [...] }`)
- ⛔ Cero `$_POST['x']` o `req.body.x` directo sin validar
- ⛔ Cero confianza en headers no firmados (`User-Agent`, `X-Forwarded-For` solo para logging, nunca para auth)

### 7. Seguridad — secrets y credenciales
- ⛔ Cero secrets hardcoded en código (sk_test_, sk_live_, password='xxx', apiKey='xxx')
- ⛔ Cero `.env`, `.env.local`, `.env.production` commiteados (deben estar en `.gitignore`)
- ✅ `.env.example` presente con TODAS las vars necesarias (vacías o con placeholders)
- ✅ Credenciales de demo se generan en setup wizard o `.env`, no en código
- ✅ Tokens de API en headers/cookies, nunca en URLs

### 8. Seguridad — autenticación
- ✅ Password hashing con **bcrypt / argon2 / scrypt / pbkdf2** — NUNCA md5/sha1/plain
- ✅ Sessions con **HttpOnly + Secure (en prod) + SameSite=Lax/Strict** cookies
- ✅ Session regeneration tras login (CI3: `regenerate_destroy`; Node: rotación de JWT; Laravel: `regenerate`)
- ✅ Rate limiting en login/signup/forgot-password (al menos 5-10 req/min por IP)
- ✅ CSRF protection activo (token en form / double-submit cookie / SameSite cookie)
- ✅ Logout invalida sesión server-side (no solo borra cookie en client)

### 9. Seguridad — comunicación y datos
- ✅ Helmet (o equivalente) configurado con headers de seguridad mínimos:
  - `X-Frame-Options: DENY` (o `SAMEORIGIN` si embebes)
  - `X-Content-Type-Options: nosniff`
  - `Referrer-Policy: strict-origin-when-cross-origin`
  - `Strict-Transport-Security` (en prod con HTTPS)
  - `Permissions-Policy` (camera, microphone, geolocation desactivados por default)
- ✅ CORS configurado con allowlist en producción (no `*` open)
- ✅ Queries parametrizadas SIEMPRE (Query Builder, prepared statements, ORM) — NUNCA string templating con input
- ✅ Output escaping en respuestas HTML (`htmlspecialchars` PHP, React escapa por default pero ojo con `dangerouslySetInnerHTML`)
- ✅ File uploads con validación de mime type + size + extensión

### 10. Manejo de errores
- ✅ Try/catch en TODAS las funciones async externas (DB queries, HTTP calls, file system)
- ✅ Error handler global en backend (Express middleware, Laravel exception handler, etc.)
- ✅ Mensajes de error al usuario son **legibles**, no stack traces
- ✅ Errores se loggean estructurados (con request ID si aplica)
- ✅ Frontend tiene error boundary (React `<ErrorBoundary>`, Vue `errorCaptured`, Angular `ErrorHandler`)
- ✅ 404/500 customizados (no default del framework)

### 11. Tests (mínimo vendible)
- ✅ Al menos **smoke tests** del endpoint de salud + auth flow
- ✅ Framework de tests configurado (Vitest/Jest/Mocha JS; PHPUnit/Pest PHP; pytest Python)
- 🎯 **Cobertura mínima**: 20% smoke + paths críticos (auth, billing si aplica)
- 🎯 **Cobertura ideal**: 50%+ en lib/services y 100% en validators
- ✅ Si hay frontend: smoke E2E (Playwright/Cypress) del happy path

### 12. Build y deploy
- ✅ Script `build` reproducible (`npm run build`, `composer install --no-dev`, etc.)
- ✅ Si producto self-hosted: **Dockerfile** + `docker-compose.yml` con healthcheck
- ✅ Alternativa para bare VPS: PM2 `ecosystem.config.cjs` (Node) o systemd unit
- ✅ `.env.example` completo con TODAS las vars
- ✅ `.gitignore` cubre: node_modules, vendor, .env*, dist, build, .DS_Store, *.log, tmp/, cache/
- ✅ Engine versions especificadas (`engines.node` en package.json; `php` en composer.json)

### 13. i18n y accesibilidad básica
- ✅ Si el producto tiene UI: **mínimo 2 idiomas** (English + un segundo: típico Spanish para LATAM)
- ✅ Strings de UI viven en archivos de translation, NO hardcoded en JSX/views
- ✅ Atributos `aria-label`, `aria-describedby`, `role` en components interactivos (modals, dialogs, dropdowns)
- ✅ `alt` text en imágenes informativas; `alt=""` en decorativas
- ✅ Foco visible en elementos interactivos (no `outline: none` sin reemplazo)
- ✅ Contraste WCAG AA mínimo (4.5:1 texto normal, 3:1 texto grande)

### 14. Documentación entregable (lo que recibe el comprador)
- ✅ **README.md** corto y útil (3-5 secciones: descripción, features, install, support)
- ✅ **INSTALL.md** o sección en README con pasos detallados (al menos 1 modo de deploy)
- ✅ **CHANGELOG.md** con versiones e fechas (v1.0.0 inicial mínimo)
- ✅ **LICENSE.txt** referenciando licencia del marketplace (Envato Regular License para CodeCanyon)
- ✅ **ARQUITECTURA.md** opcional pero altamente recomendado — explica decisiones técnicas
- ✅ **STYLE-GUIDE.md** opcional pero recomendado si hay UI propia — paleta, tipografía, componentes
- 🎯 **Bonus**: documentación HTML in-product (`/docs/` con MDX) o Quick Start PDF

### 15. Coherencia y profesionalismo general
- ✅ Versión del producto en `package.json`/`composer.json` coincide con `CHANGELOG.md`
- ✅ Cabeceras HTTP `X-Powered-By` desactivadas (no revelar versión del framework)
- ✅ Endpoints `/health`, `/api/health` o equivalente para healthchecks
- ✅ Si tiene admin panel: protegido por permisos, no solo "URL secreta"
- ✅ Si tiene multi-usuario: row-level access (cada usuario ve solo lo suyo) **independiente** del framework de auth
- ⛔ Cero placeholders `Lorem Ipsum` en seed data o views entregables
- ⛔ Cero links a `localhost`, `127.0.0.1`, o IPs internas en strings de producción
- ⛔ Cero referencias a otros proyectos del dev (typos, copy-paste de otro repo)

## Anti-patterns universales (señales de NO-vendible)

Si detectas 3+ de estos en un producto, hay un problema sistémico de calidad:

1. `console.log` / `var_dump` esparcidos en 5+ archivos
2. Carpetas `_old/`, `backup/`, `tmp/` commiteadas
3. Archivos comentados completos en folders core
4. README de 5 líneas tipo "install dependencies and run"
5. Sin `.env.example` (o uno incompleto, faltan vars)
6. Credenciales de admin hardcoded en seed/install (`admin@admin.com / admin123` sin marcarlo como "cambiar")
7. SQL con interpolación directa de variables sin parametrizar
8. Funciones de 200+ líneas (un solo archivo con todo)
9. Imports relativos profundos `../../../../utils/x.js`
10. Naming inconsistente entre carpetas (`auth.controller.js` y `userController.js` en el mismo proyecto)
11. Sin tests, sin framework de tests configurado
12. Strings de UI en inglés + comentarios en español mezclados
13. Sin `.gitignore` o uno mínimo (commitea node_modules/, .env, etc.)
14. Sin manejo de errores (`throw` sin catch, errors silenciados)
15. Sin documentación de cómo desplegar a producción

## Stack-specific add-ons

Cuando este skill se invoca, **adicionalmente** consulta el canonical específico si el stack encaja:

| Si el stack es… | Carga también… |
|------------------|----------------|
| Perfex módulo | `envato-canonical-pattern` |
| CI3 standalone (NO Perfex) | `ci3-canonical-pattern` |
| Next.js + Supabase + TS | `node-canonical-pattern` |
| Express + React SPA + SQLite/MySQL | Solo este universal (es válido) |
| NestJS API standalone | `node-canonical-pattern` (sección Node API puro) |
| Angular standalone | `node-canonical-pattern` (sección Angular) |
| Vue/Nuxt | Solo este universal (no tenemos canonical específico aún) |
| Laravel monolito | Solo este universal (no tenemos canonical Laravel aún) |
| Python/Django o FastAPI | Solo este universal |
| Go (net/http, Gin, Echo) | Solo este universal |
| Ruby on Rails | Solo este universal |

**Importante**: NO inflar el report con reglas que no aplican al stack. Si el
producto es JS sin TypeScript, NO marques `tsconfig strict` como fallo —
simplemente no aplica.

## Métrica de salud global

Para reportar al usuario un veredicto rápido, mapea las 15 dimensiones contra
el producto y calcula:

- **15/15 ✅** → 🟢 GO — Listo para submit
- **13-14/15 ✅** → 🟢 GO con warnings menores
- **10-12/15 ✅** → 🟡 WARN — Atender mayors antes de submit
- **7-9/15 ✅** → 🟡 WARN — Trabajo significativo pendiente
- **<7/15 ✅** → 🔴 STOP — No vendible en estado actual

## Plantilla del reporte que generas

```markdown
# Auditoría Universal: <Product Name>

## Stack detectado
- Lenguaje principal: <JS / TS / PHP / Python / Go / etc.>
- Framework frontend: <React / Vue / Angular / vanilla / N/A>
- Framework backend: <Express / Next / Laravel / Django / etc.>
- Base de datos: <SQLite / MySQL / Postgres / Supabase / etc.>
- Auth: <propio / Supabase / Auth0 / NextAuth / etc.>
- Idiomas soportados: <N>

## Veredicto global
🟢 GO / 🟡 WARN / 🔴 STOP — <N>/15 dimensiones pasan

## Score por dimensión
| # | Dimensión | Estado | Notas |
|---|-----------|--------|-------|
| 1 | Estructura y arquitectura | ✅ / ⚠️ / 🔴 | <1 línea> |
| 2 | Naming consistency | ... | ... |
| ... | ... | ... | ... |
| 15 | Coherencia general | ... | ... |

## Issues críticos (🔴)
- <archivo:línea> — <problema> — <fix sugerido>

## Issues mayores (⚠️)
- <archivo:línea> — <problema> — <fix sugerido>

## Issues menores (ℹ️)
- <archivo:línea> — <problema> — <fix sugerido>

## Puntos fuertes (cosas que destacan positivamente)
- <1-5 ítems>

## Próximos pasos
- Si 🟢: opcional `/preparar-venta`
- Si 🟡: atender mayors primero, re-auditar
- Si 🔴: lista priorizada de trabajo
```

## Cuándo este skill se invoca

- En el agente `universal-code-auditor` (siempre)
- En `code-reviewer-pro` cuando el stack no encaja en canonical específicos
- En `codecanyon-qa` cuando se audita producto híbrido o no-mainstream stack
- Cuando el usuario corre `/auditar-codigo` (sin argumentos de stack)
- Cuando el usuario pregunta "¿qué tan limpio está mi código?"

## Versionado

- v1.0 (2026-05-21): inicial, basado en análisis empírico de 4 productos referencia
  (DupliGuard / TicketYA / Framecast / WhatsAppQR) + criterios universales Envato 2026.
