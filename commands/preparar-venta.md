---
description: Invoca codecanyon-release para empaquetar el módulo (recursos de venta + screenshots + ZIP final).
---

Invoca al agente `codecanyon-release` para empaquetar el módulo del cwd actual
para venta en CodeCanyon.

## Workflow

1. Identifica la ruta del módulo:
   - cwd actual (debería ser `D:\laragon-6.0.0\www\<nombre>\`).
   - Si el usuario pasó argumentos `$ARGUMENTS`, esa es la ruta.

2. **Pre-condiciones**: verifica que pasó por QA. Busca archivo de reporte QA
   reciente o invoca `codecanyon-qa` brevemente para confirmar 🟢. Si no pasa
   QA, NO sigas — reporta al usuario.

3. Invoca `Agent` con `subagent_type=codecanyon-release`:

```
Empaqueta el módulo en <ruta-codigo> para venta en CodeCanyon.

Carpeta de venta: D:\ventas\<nombre>\

Genera:
- recursos/codecanyon/ con los 11 archivos textuales
- imagenes/ con screenshots si la demo está corriendo (Chrome DevTools MCP)
- ZIP final excluyendo dev artifacts

Reporta en tu formato (resumen + pendientes + instrucciones de upload).
```

4. Pasa el reporte completo al usuario.

5. Recordatorio final al usuario sobre los assets gráficos pendientes (icono,
   banner, cover) — esos requieren que él los genere con los prompts.

## Reglas

- NO subes nada a CodeCanyon. Solo preparas.
- Si la demo no está corriendo (no hay `D:\laragon-6.0.0\www\perfex\` con el módulo
  instalado), avisa al usuario que los screenshots quedaron pendientes.
- Sé claro sobre qué QUEDA en sus manos: subir el ZIP, generar los 3 PNGs con
  IA, copiar el contenido HTML a CodeCanyon.
