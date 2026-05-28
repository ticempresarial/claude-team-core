---
description: Investiga items de CodeCanyon (Envato Market) usando chrome-devtools MCP. Cloudflare NO bloquea porque es Chrome real, no scraper. Extrae precio, ventas, rating, tags, features y comments públicos. Útil para validar pricing antes de lanzar, comparar con competencia, entender por qué un producto fue rechazado, o investigar un seller. Throttle 3-5s entre items, máximo 10 items por sesión (respeta TOS Envato).
argument-hint: "<keyword|url|seller>" [N items]
---

Vas a investigar competencia en CodeCanyon usando chrome-devtools MCP (Chrome real
controlado por DevTools Protocol — Cloudflare NO lo bloquea porque NO es scraper).

**Argumentos**: $ARGUMENTS

## Parseo

- 1 argumento: úsalo como query
  - Si empieza con `https://codecanyon.net/` → URL directa de item
  - Si contiene `/user/` → seller portfolio
  - Sino → keyword de búsqueda
- 2 argumentos: primero query, segundo cantidad N (default 5, max 10)
- Sin argumentos: pedir keyword/URL al usuario

## Pre-flight check del MCP

Antes de invocar el agente, verifica que chrome-devtools MCP esté disponible:

1. Llama `mcp__chrome-devtools__list_pages`
2. Si falla: indica al usuario "El MCP chrome-devtools no está disponible.
   Verifica con `/check-mcp` o ejecuta `/setup-mcp` en el proyecto."
3. Si responde: procede.

## Setup del Chrome con bypass de Cloudflare

Antes del análisis del item objetivo:

1. **Warmup**: navega primero a `https://codecanyon.net` (página principal)
2. `wait_for` el load complete (espera 3-5 segundos)
3. `take_snapshot` para confirmar estado:
   - Si snapshot contiene "Just a moment..." o "Verify you are human":
     - Esperar 8 segundos
     - Re-snapshot
     - Si persiste: **PARAR** y pedir al usuario:
       > "Cloudflare está pidiendo verificación. Abre Chrome (el que MCP controla),
       > pasa el challenge manualmente, luego confirma para reintentar."
4. Una vez en página normal, Cloudflare emite cookie `cf_clearance` y las siguientes
   navegaciones pasan sin challenge.

## Invocación del agente

```
Agent(subagent_type="codecanyon-researcher", prompt="
Investiga competencia CodeCanyon.

Query: <query>
Cantidad de items a analizar: <N> (default 5, max 10)
Objetivo: <competencia general | comparar con producto X | entender rechazo>

PASO 1: Warmup en codecanyon.net para pasar Cloudflare.
PASO 2: Búsqueda o navegación directa según query.
PASO 3: Para cada item top N: navegar, snapshot, extraer precio/ventas/rating/tags/features/stack.
PASO 4: Análisis comparativo en tabla.
PASO 5: Conclusiones accionables.

THROTTLE obligatorio: 3-5 segundos entre navegaciones.
NUNCA más de 10 items por sesión.

Reporta en markdown estructurado max 1500 palabras.
")
```

## Casos de uso típicos

### 1. Investigar competencia antes de lanzar producto
```
/investigar-competencia "whatsapp inbox saas"
```
→ Top 5 items con análisis de precio, features, tags, gaps de mercado.

### 2. Analizar un item específico en profundidad
```
/investigar-competencia "https://codecanyon.net/item/whatswaybusinessbulkwhatsappmarketingapplication/41615678"
```
→ Análisis completo: features, ventas, rating, reviews, stack.

### 3. Investigar portafolio de un seller
```
/investigar-competencia "https://codecanyon.net/user/maestricsolutions/portfolio"
```
→ Sus items con métricas + categorías que cubre.

### 4. Comparar 10 productos similares
```
/investigar-competencia "perfex crm module" 10
```
→ Top 10 con tabla comparativa + identificación de gaps.

### 5. Entender por qué tu producto fue rechazado
```
/investigar-competencia "<categoría de tu producto>" 5
```
→ Cuando devuelva, le dices: "compara estos 5 aprobados con mi producto en
[path]. Dime qué hacen distinto y por qué pudieron rechazarme."

## Reglas

- ⛔ NO scraping masivo (max 10 items por sesión)
- ⛔ NO navegar a páginas privadas (dashboard del seller, analytics)
- ⛔ NO automatizar compras
- ✅ Throttle 3-5s entre navegaciones
- ✅ Si Cloudflare persiste con challenge, parar y pedir intervención manual
- ✅ Si quieres profundizar después en un item específico, ejecutar el comando de nuevo

## Output esperado

Reporte estructurado con:
- Resumen ejecutivo (precio promedio, saturación, ventas top)
- Tabla comparativa por item
- Detalle por item (URL, precio, ventas, features, stack, demo)
- Análisis: features comunes vs diferenciadores
- Conclusiones accionables para tu producto

## Slash commands relacionados

- `/check-mcp` — verifica que chrome-devtools MCP esté online antes de empezar
- `/setup-mcp` — si no está, configurar
- `/auditar-codigo` — después de investigar competencia, audita tu producto contra los hallazgos
