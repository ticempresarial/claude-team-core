---
name: c4-memory
description: Mantén una memoria ligera del agente sobre contexto, configuración y estado operativo sin introducir dependencias externas.
---

# C4 Memory

Usa esta skill cuando necesites preservar contexto operativo entre sesiones.

## Qué guardar

- Configuración activa por proyecto.
- MCP y perfiles habilitados.
- Decisiones de arquitectura del agente.
- Riesgos abiertos o trabajo pendiente.

## Dónde guardarlo

- Contexto durable: `README.md`, `CLAUDE.md`, `docs/`
- Contexto operativo corto: `local/retomar/`
- Estado reproducible: `.mcp.json`, `.claude/settings.json`, scripts del repo

## Qué evitar

- Memoria implícita en servicios externos.
- Notas duplicadas en múltiples lugares.
- Datos sensibles o personales.
