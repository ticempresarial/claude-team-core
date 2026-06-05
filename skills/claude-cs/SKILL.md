---
name: claude-cs
description: Modo de cirugía de código para cambios pequeños y medianos, con foco en seguridad, idempotencia y limpieza del diff.
---

# Claude CS

Usa esta skill cuando estés implementando o corrigiendo código del agente.

## Reglas

1. Haz cambios pequeños, reversibles y fáciles de revisar.
2. Prefiere configuración declarativa sobre pasos manuales.
3. Si agregas automatización, debe degradar con elegancia cuando falte una dependencia.
4. Antes de cerrar, valida sintaxis, JSON y el flujo mínimo real.
5. Si algo no es portable entre máquinas, muévelo a variables de entorno o a `local/`.

## Checklist

- El cambio funciona sin `jq`.
- No depende de repos privados para arrancar.
- No rompe proyectos que ya tengan `.mcp.json` o `.claude/`.
- La documentación refleja el flujo real.
