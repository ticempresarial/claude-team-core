---
description: Audita cualquier producto (cualquier stack — PHP, Node, Python, Go, React, Angular, Vue, monorepos híbridos) contra las 15 dimensiones de calidad de código UNIVERSAL. Agnóstico — NO penaliza por elección de stack. Devuelve veredicto 🟢/🟡/🔴 con score por dimensión + issues por severidad + puntos fuertes. Si el stack además encaja en un canonical específico, recomienda comando complementario.
argument-hint: "<path opcional>"
---

Vas a auditar un producto contra los estándares de **calidad de código UNIVERSAL**
del skill `universal-code-quality`. Este audit es agnóstico de stack — no
penaliza por elegir React SPA en lugar de Next.js, o Express en lugar de NestJS.

**Argumentos**: $ARGUMENTS

## Parseo

- Si viene un path: úsalo como cwd a auditar
- Si no viene: usa el cwd actual
- Si el cwd no parece tener un proyecto (sin package.json/composer.json/etc.): pregunta al usuario

## Flujo de auditoría

### PASO 1 — Invocar al auditor universal
```
Agent(subagent_type="universal-code-auditor", prompt="
Audita producto en <path> contra las 15 dimensiones de calidad universal del
skill universal-code-quality.

Detecta stack automáticamente. NO penalices por elección de framework.
Si el stack encaja en un canonical específico (node-canonical-pattern,
ci3-canonical-pattern, envato-canonical-pattern), recomienda al final
ejecutar el comando complementario (/auditar-node, /auditar-ci3, /validar-codecanyon).

Reporta:
- Stack detectado
- Veredicto global con score N/15
- Score por dimensión en tabla
- Issues por severidad (critical / major / minor)
- Puntos fuertes
- Anti-patterns detectados (checklist de 15)
- Aplicabilidad de canonical patterns
- Próximos pasos accionables

Máximo 800 palabras. Sample-based si el producto es grande (>10k LOC).
")
```

### PASO 2 — Presentar reporte al usuario

Cuando el auditor termine, presenta el reporte tal cual.

Si el auditor recomendó un comando complementario (`/auditar-node`, `/auditar-ci3`,
`/validar-codecanyon`), preguntar al usuario si quiere ejecutarlo también ahora.

### PASO 3 — Si veredicto es 🟡 o 🔴

Ofrecer al usuario:
1. Ver detalle de un issue específico
2. Auditar a profundidad un módulo concreto
3. Continuar con código fixeado (volver a ejecutar `/auditar-codigo` después)

## Diferencia con otros commands

| Command | Cuándo usar |
|---------|-------------|
| `/auditar-codigo` (este) | **Default** cuando no sabes el stack o es híbrido. Agnóstico. |
| `/auditar-node` | Producto Node con stack mainstream (Next.js o NestJS bien definido) |
| `/auditar-ci3` | Producto CI3 standalone (NO Perfex) |
| `/validar-codecanyon` | Módulo Perfex CRM específico |

Cuando dudes, **usa este** primero. El reporte te dirá si conviene ejecutar
también uno de los específicos.

## Reglas

- NO modificar código del producto
- NO invocar `codecanyon-release` ni `/preparar-venta` automáticamente
- NO penalizar por stack elegido
- Reporte conciso (máximo 800 palabras del agente)
- Si el producto es muy grande (>30k LOC), advertir que es sample-based

## Casos de uso típicos

1. **"Tengo este proyecto en JS+React+Express y no sé si está listo"** → `/auditar-codigo`
2. **"Compré un producto Node como referencia, ¿qué tan bueno está?"** → `/auditar-codigo` + `/auditar-node` complementario
3. **"Este monorepo con apps Angular + NestJS + shared libs"** → `/auditar-codigo` cada sub-app
4. **"Producto Laravel que vendo a clientes directos"** → `/auditar-codigo` (no tenemos canonical Laravel todavía)
5. **"Pre-CodeCanyon check general"** → `/auditar-codigo` primero, luego stack-specific si aplica
