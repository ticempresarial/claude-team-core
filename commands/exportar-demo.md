---
description: Invoca al demo-data-exporter para generar un archivo de seed listo para subir a producción demo público (limpio, sin markers visibles, multi-stack). Auto-detecta stack del cwd o usa argumento.
argument-hint: [stack] [output-path]
---

Vas a invocar al `demo-data-exporter` para generar un archivo de seed
ejecutable que el usuario sube a su servidor de producción.

**Argumentos**: $ARGUMENTS

## Parseo

- Sin argumentos: auto-detecta stack del cwd, output = `./demo-seed.<ext>`
- 1 argumento: si parece stack (php, laravel, prisma, drizzle, postgres,
  mysql, flutter), úsalo como stack; si parece path, úsalo como output
- 2 argumentos: primero stack, segundo output path

Stacks válidos: `perfex`, `laravel`, `prisma`, `drizzle`, `typeorm`,
`sequelize`, `postgres`, `mysql`, `sqlite`, `flutter`.

## Invocación

```
Agent(subagent_type="demo-data-exporter", prompt="
Genera archivo de seed para producción demo público del proyecto en el cwd actual.

Stack: <auto-detectado o argumento>
Output path: <argumento o ./demo-seed.<ext>>

Sigue estrictamente el skill `demo-data-strategies`:
- Nombres realistas en campos públicos (NO Lorem, NO [DEMO])
- Marker [INTERNAL_SEED] SOLO en campos internos (adminnote, notes)
- Distribución realista de estados y fechas
- Idempotente (INSERT IGNORE / ON CONFLICT / skipDuplicates)
- Incluye bloque de verificación al final
- Incluye header con instrucciones de uso

Lee el código del módulo/app para identificar TODAS las entidades
(incluidas las específicas del módulo, ej. tbl<modulo>_rules) y genera
data para cada una.

NO ejecutes el seed. Solo genera el archivo.
NO modifiques código del módulo. Solo lees.
")
```

## Reporte al usuario

Cuando el exporter termine, presenta:

```
📦 Demo seed generado

Archivo: <path>
Stack: <stack>
Tamaño: <KB>
Entidades: <N>
Filas totales: <X>

## Cómo subirlo a producción

<comando específico al stack>

## Cómo verificar después de ejecutar

<query de verificación>

## Cómo limpiar después

<DELETE statements>
```

## Reglas

- NO invocar a otros agentes. Solo al exporter.
- Si el cwd no tiene un proyecto identificable, pide al usuario que se
  posicione en la carpeta del módulo/app.
- Si el output file ya existe, el exporter pregunta sobrescribir o
  agregar timestamp. Respeta esa decisión.
