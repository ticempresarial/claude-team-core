# Guía de mejora continua del equipo IA

Este documento es **fuente de verdad** para mantener, mejorar y versionar los
equipos IA de ticempresarial (`claude-team-core` + los 5 stack-específicos).

> **Filosofía**: cada lección que aprendes en un proyecto real (rechazo de
> Envato, bug detectado tarde, feedback del comprador, mejor práctica
> descubierta) se convierte en un commit en el repo del equipo apropiado.
> En 6-12 meses tu equipo será drásticamente mejor que hoy.

---

## 1. Cuándo mejorar un agente/skill/command

| Trigger | Qué hacer |
|---------|-----------|
| **Rechazo en CodeCanyon** | Documentar la razón en el skill apropiado + ajustar el agente que debió detectarlo |
| **Aprobación con feedback** | Si el reviewer mencionó algo a mejorar, codificarlo |
| **Bug encontrado en producción** | Agregar verificación en el agente que debió prevenirlo |
| **Idea de un comprador real** | Si es valiosa, sumarla al backlog del equipo (GitHub Issue) |
| **Nuevo patrón descubierto** | Si vale para futuros productos, codificarlo como skill o regla |
| **Producto de la competencia mejor que el tuyo** | Analizar qué hace bien, codificar el patrón |
| **Output del agente fue malo** | Refinar su prompt para que la próxima salga mejor |

---

## 2. Workflow de mejora — 7 pasos

### Paso 1 — Identificar el repo correcto
- ¿Aplica a TODOS los stacks (Perfex, CI3, Node, Laravel, WP)? → `claude-team-core`
- ¿Aplica solo a un stack específico? → `claude-team-<stack>`

### Paso 2 — Clonar/actualizar el repo localmente
```bash
cd D:\proyectos-claude\claude-team-core
git pull origin main
```

Si nunca lo clonaste:
```bash
cd D:\proyectos-claude
git clone https://github.com/ticempresarial/claude-team-core.git
cd claude-team-core
```

### Paso 3 — Crear branch experimental (opcional pero recomendado para cambios grandes)
```bash
git checkout -b mejora/qa-gatekeeper-detecta-cron-path
```

Si es un fix pequeño (typo, regla nueva), puedes editar directo en `main` sin branch.

### Paso 4 — Editar el archivo
Abre VS Code:
```bash
code .
```

Y edita el agente / skill / command. Por ejemplo:
- Mejorar un agente: `agents/qa-gatekeeper.md` → agregar verificación nueva en su prompt
- Mejorar un skill: `skills/universal-code-quality/SKILL.md` → agregar regla nueva
- Mejorar un command: `commands/auditar-codigo.md` → ajustar flujo

### Paso 5 — Commit descriptivo
Buen mensaje de commit explica QUÉ y POR QUÉ:

```bash
git add -A
git commit -m "qa-gatekeeper: detect absolute server paths in views

After TaskGuard rejection (2026-05) por exponer
'/home/user/cron.php' en banner del dashboard, agregar al gate
visual la verificación de paths absolutos en cualquier view.

Test: grep recursivo de /home/, C:\\, D:\\ en application/views/.
Cualquier match = 🔴 en Gate 2 visual."
```

Estructura recomendada:
- Primera línea: `<componente>: <verbo presente> <qué>`
- Línea en blanco
- Cuerpo: contexto del problema + qué se cambió + cómo probarlo

### Paso 6 — Push
```bash
git push origin main
```

O si trabajaste en branch:
```bash
git push origin mejora/qa-gatekeeper-detecta-cron-path
# luego crear PR en GitHub para merge a main cuando esté probado
gh pr create --title "qa-gatekeeper: detect absolute server paths" --body "..."
```

### Paso 7 — Versionar si el cambio es significativo
```bash
# Para fix menor (typo, regla, prompt tuning) — no necesitas tag
# Para feature nueva o cambio importante:
git tag -a v0.3.0 -m "Add absolute path detection to qa-gatekeeper"
git push origin v0.3.0
```

---

## 3. Versionado SemVer

Sigue [Semantic Versioning](https://semver.org/lang/es/):

| Versión | Cuándo |
|---------|--------|
| **MAJOR** (v1.0.0 → v2.0.0) | Cambio breaking: renombras un agente que productos usan, eliminas un skill, cambias estructura del repo |
| **MINOR** (v1.0.0 → v1.1.0) | Feature nueva: agregas un agente nuevo, agregas un skill, agregas un command |
| **PATCH** (v1.0.0 → v1.0.1) | Fix: typo en un prompt, regla nueva en un skill existente, bug fix en un command |

### Cuándo cortar release estable
- `v0.x.y` = en construcción. Puedes romper cosas sin avisar.
- `v1.0.0` = primer release estable. **De aquí en adelante, breaking changes requieren MAJOR bump.**

### Cómo cortar release
```bash
# 1. Actualizar CHANGELOG.md con la sección [X.Y.Z]
# 2. Commit del CHANGELOG
git add CHANGELOG.md && git commit -m "chore: release v0.3.0"

# 3. Tag anotado
git tag -a v0.3.0 -m "v0.3.0: Add absolute path detection"

# 4. Push commit + tag
git push origin main --tags

# 5. (Opcional) crear GitHub Release desde la web o con gh
gh release create v0.3.0 --title "v0.3.0: Absolute path detection" --notes-file CHANGELOG.md
```

---

## 4. Cómo se propagan los cambios a tus proyectos

### Modo auto-update (default)
Los proyectos con `.claude/settings.json` sin pin de versión reciben siempre la última versión:
```json
{
  "enabledPlugins": [
    "claude-team-core",
    "claude-team-perfex"
  ]
}
```

### Modo pineado (productos en venta)
Para productos ya aprobados en CodeCanyon, mejor congelar versión:
```json
{
  "enabledPlugins": [
    "claude-team-core@v1.0.0",
    "claude-team-perfex@v1.0.0"
  ]
}
```

Cuando manualmente decidas que ese producto debe usar mejoras nuevas, actualizas el pin.

---

## 5. Workflow para experimentos grandes

Si quieres probar un cambio que puede romper cosas:

```bash
# 1. Branch nuevo
git checkout -b experimento/rewrite-architect

# 2. Cambios grandes — refactor del prompt del architect
# (edita archivos)

# 3. Pruebas en 1 proyecto piloto
# - En el .claude/settings.json del proyecto piloto, apunta al branch:
#   { "enabledPlugins": ["claude-team-perfex@experimento/rewrite-architect"] }
# - Trabajas algunos días con eso

# 4a. Si funciona → merge a main
git checkout main
git merge experimento/rewrite-architect
git push origin main

# 4b. Si no funciona → simplemente borra el branch
git checkout main
git branch -D experimento/rewrite-architect
```

---

## 6. Lecciones aprendidas → carpeta `memory/`

Cada vez que un evento real te enseña algo nuevo, agrégalo a un archivo en
`memory/`. Estos archivos son visibles para los agentes durante su trabajo y
les dan contexto histórico.

Formato sugerido (`memory/lessons-learned.md`):

```markdown
## 2026-05-20 — Banner cron con path absoluto = rechazo Envato

**Contexto**: TaskGuard rechazado en CodeCanyon.

**Causa raíz**: el dashboard mostraba textualmente:
'* * * * * php /home/user/public_html/cron.php'

**Por qué falla**: Envato lo considera information disclosure.

**Fix aplicado**: qa-gatekeeper ahora hace grep recursivo de paths absolutos
en `application/views/` antes de aprobar Gate 2.

**Referencia**: TaskGuard module, archivo `views/partials/cron_status_banner.php:40`.
```

---

## 7. Mejorar agentes — guía específica

### Reconocer cuándo un prompt necesita mejora
- El agente da output mediocre repetidamente
- El agente se sale del scope (hace cosas que no debería)
- El agente no sigue el formato esperado
- El agente no detecta un problema obvio

### Cómo refinar un prompt
1. Identifica el ejemplo donde falló
2. Pregunta: ¿qué instrucción explícita evitaría este fallo?
3. Agrega esa instrucción al agente en `agents/<nombre>.md`
4. Si es una regla "duro NO", márcalo con ⛔ o "NUNCA hagas X"
5. Si es una guía de estilo, agrégala como ejemplo concreto

### Anti-patterns en prompts (evítalos)
- ❌ Prompts genéricos sin ejemplos concretos
- ❌ Listas de 50+ reglas sin priorizar (el agente las ignora)
- ❌ Contradicciones internas (decir "haz X" y luego "no hagas X" en otro párrafo)
- ❌ Vocabulario inconsistente (a veces "review", a veces "audit", a veces "check")
- ❌ Falta de contexto sobre cuándo invocar al agente

---

## 8. Mejorar skills — guía específica

### Estructura de un skill
```
skills/mi-skill/
└── SKILL.md
```

`SKILL.md` empieza con frontmatter:
```yaml
---
name: mi-skill
description: Breve descripción de qué hace y cuándo activarlo
---
```

### Cuándo agregar regla nueva
- Una regla nueva surge cuando descubres que cierta práctica evita un problema recurrente
- O cuando un patrón se aplica a múltiples productos y debe codificarse

### Estructura recomendada de skill
1. **Filosofía** (1 párrafo)
2. **Cuándo aplicar este skill**
3. **Reglas duras** (no negociables) con número y justificación
4. **Anti-patterns** (qué NO hacer)
5. **Ejemplos concretos** (con código)
6. **Checklist verificable**

---

## 9. Mejorar slash commands — guía específica

### Cuándo agregar un command nuevo
- El flujo se ejecuta repetidas veces y vale la pena automatizar
- Existe un patrón de invocación que más de 3 productos usarán

### Cuándo modificar un command existente
- El flujo cambió (agregaste un paso, eliminaste uno)
- Quieres que invoque un agente distinto
- Quieres que el reporte tenga formato distinto

### Anti-patterns en commands
- ❌ Commands que hacen demasiadas cosas (mejor 2 commands chicos que 1 monolito)
- ❌ Commands sin argumentos cuando deberían tenerlos
- ❌ Commands que invocan a agentes sin pasar contexto

---

## 10. CI con GitHub Actions (opcional, para automatizar validaciones)

Cuando el equipo crece, conviene validar automáticamente cada push:

`.github/workflows/validate.yml`:
```yaml
name: Validate plugin format
on: [push, pull_request]
jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Validate plugin.json schema
        run: |
          python -c "import json; json.load(open('.claude-plugin/plugin.json'))"
      - name: Check all agents have valid frontmatter
        run: |
          # Validar que cada agents/*.md tiene name: y description:
          for f in agents/*.md; do
            head -5 "$f" | grep -q "^name:" || (echo "Missing 'name:' in $f" && exit 1)
            head -5 "$f" | grep -q "^description:" || (echo "Missing 'description:' in $f" && exit 1)
          done
```

---

## 11. Resumen ejecutivo

| Acción | Comando |
|--------|---------|
| Actualizar repo local | `git pull origin main` |
| Editar agente | `code agents/<nombre>.md` |
| Commit descriptivo | `git commit -m "<scope>: <que>"` |
| Push | `git push origin main` |
| Release nueva versión | `git tag -a vX.Y.Z -m "..."` + `git push --tags` |
| Experimento grande | `git checkout -b experimento/<nombre>` |
| Revertir cambio que rompió | `git revert <commit-hash>` |
| Ver historial de un archivo | `git log -p agents/<nombre>.md` |

---

## 12. Pregúntate antes de commitear

- [ ] ¿El cambio mejora algo concreto o es solo "estética"?
- [ ] ¿El cambio puede romper algún producto que ya usa el plugin?
- [ ] ¿Documenté en el mensaje QUÉ cambió y POR QUÉ?
- [ ] ¿Si es breaking, bump MAJOR + warning en CHANGELOG?
- [ ] ¿Si es feature, bump MINOR + sección en CHANGELOG?
- [ ] ¿Si es regla nueva en skill, agrega ejemplo concreto?
- [ ] ¿Si es lección aprendida, va a `memory/`?

---

*Última actualización: 2026-05-28 — guía v1.0*
