---
name: comment-style-guide
description: Patrón canónico de comentarios para módulos Perfex CRM extraído de DupliGuard (aprobado en CodeCanyon). Define ratios objetivo, plantillas y anti-patrones para CSS, PHP, JS y archivos de lang. Activa cuando un agente esté generando o revisando código y necesite decidir cuándo/cómo comentar.
---

# Guía de comentarios — patrón DupliGuard

Este skill documenta el estándar de comentarios validado por la aprobación de
**DupliGuard v1.1.1** en CodeCanyon (vendor `ticempresarial`). Se usa al
**generar** código (perfex-module-builder) y al **revisar** código
(code-reviewer-pro).

## Filosofía (regla de oro)

> **Una regla CSS aislada, un getter trivial o una función de 3 líneas NO
> necesitan comentario. Un BLOQUE funcional (componente, módulo, orquestador,
> método con efectos secundarios) SÍ — 1-3 líneas encima diciendo QUÉ es ese
> bloque, no qué hace cada línea.**

El comentario sirve al lector que quiere navegar el archivo, no al que ya
está leyendo cada línea. Comenta para **escanear**, no para narrar.

Anti-patrón mortal:
```php
// Set $name to 'John'           ← NO. El código ya lo dice.
$name = 'John';
```

Patrón correcto:
```php
// Cache the staff list 5 min — DataTable AJAX hits this on every page.
$cached = get_cached_staff();    ← Explica QUÉ y POR QUÉ, no QUÉ hace la línea
```

## Ratios objetivo (medidos en DupliGuard)

| Tipo | Ratio comentarios/LOC | Rango aceptable |
|------|----------------------|-----------------|
| CSS | **~10%** | 8-12% |
| PHP controllers | **~16%** | 14-20% |
| PHP models | **~18%** | 14-22% (>25% es sobre-comentado) |
| JS | **~10%** | 8-12% |
| Lang | **~5%** | 4-7% |

Fuera de rango = revisar:
- **Sub-comentado** (<50% del ratio): bloques sin contexto, imposible escanear.
- **Sobre-comentado** (>25% en PHP models): docblocks de 8+ líneas para
  getters, ruido que distrae.

---

## CSS — patrón canónico

### Estructura del archivo

1. **Header del archivo** (siempre): docblock con TOC numerada.
2. **Separador por componente**: `/* ════ N. NOMBRE ════ */` antes de cada
   bloque funcional (card, modal, hero, sidebar, KPI, timeline, etc.).
3. **Comentario inline solo si**: hack/workaround, valor mágico, soporte
   navegador antiguo.

### Plantilla del header

```css
/**
 * <Modulo> — Stylesheet
 *
 * TABLE OF CONTENTS
 * 1. Variables / scope
 * 2. Page reset + focus rings
 * 3. Hero banner
 * 4. KPI cards
 * 5. Generic card surface
 * 6. Timeline
 * 7. Responsive
 *
 * @package <Modulo>
 */
```

### Plantilla del separador por componente

```css
/* ════════════════════════════════════════════════════════════════
 * 4. KPI CARDS
 * Dashboard summary tiles — large number, label, icon. Hover lifts
 * the card with a soft shadow.
 * ════════════════════════════════════════════════════════════════ */

.<prefijo>-kpi-card { ... }
.<prefijo>-kpi-card__number { ... }
.<prefijo>-kpi-card__label { ... }
```

### Anti-patrones CSS

🔴 **Markers de "revert" sin explicar qué es el bloque**:
```css
/* BrandKit Pro Proposal v2 START */
.bkp-side-active-pill { ... }
/* BrandKit Pro Proposal v2 END */
```
→ Esto solo dice "esto se puede revertir" pero no qué COMPONENTE es. Falta
el header `/* ═══ X. ACTIVE SIDEBAR PILL ═══ */` con descripción.

🔴 **Header del archivo gigante + 2000 líneas sin un solo separador**:
TOC al inicio, vacío adentro. El lector escanea pero no encuentra anclajes.

🔴 **Comentario por regla individual**:
```css
.card { color: red; }    /* Set card color */    ← Ruido.
```

---

## PHP — patrón canónico

### Docblock del archivo (obligatorio en todos los .php del módulo)

```php
<?php

defined('BASEPATH') or exit('No direct script access allowed');

/**
 * <Modulo> — <Rol del archivo en una línea>
 *
 * <Descripción 2-4 líneas: qué responsabilidad tiene, qué NO hace.
 * Si es read-only, dilo. Si tiene side effects, dilo.>
 *
 * @package <Modulo>
 */
```

### Docblock de clase (controllers, models)

Conciso. Una línea por rol + lista de rutas/responsabilidades.

```php
/**
 * Dupliguard — Main Controller
 *
 * Renders the dashboard (KPI cards + last-scan banner) and the filtered
 * list of detected duplicate groups. Pure read-only — mutations live in
 * Dupliguard_scan / Dupliguard_groups.
 *
 * @package DupliGuard
 */
class Dupliguard extends AdminController
```

### Docblock de método público

**1-2 líneas máximo**. El nombre del método cubre el 80%. Solo añade
contexto si no es obvio.

```php
/**
 * Mark a duplicate group as resolved without merging.
 */
public function dismiss($group_id)
```

❌ NO hagas docblocks de 8 líneas para un getter trivial:
```php
/**
 * Get user by ID
 *
 * This method retrieves the user from the database
 * using the user_id parameter passed to it.
 *
 * @param int $id The user ID
 * @return object The user object
 */
public function get_user($id) { ... }
```
→ Reduce a 1 línea o elimina (el nombre `get_user($id)` ya dice todo).

### Separadores de sección en models

Para agrupar métodos relacionados:

```php
// ── Read operations ──

public function get_all() { ... }
public function get_by_id($id) { ... }

// ── Mutations ──

public function create($data) { ... }
public function update($id, $data) { ... }

// ── Internal helpers ──

private function normalize_input($data) { ... }
```

### Comentarios inline

Solo si la lógica no es obvia. Explica **por qué**, no **qué**.

```php
// Default to "actionable" (pending) — resolved groups clutter the view
// after a few scans.
$status = $this->input->get('status') ?? 'pending';
```

### Anti-patrones PHP

🔴 **Docblock que duplica el nombre del método**:
```php
/**
 * Save settings
 *
 * Saves the settings.
 */
public function save_settings()
```

🔴 **`@param` y `@return` para tipos triviales en docblock de 10 líneas
de método de 3 líneas**.

🔴 **Comentarios narrativos línea por línea**:
```php
// Get the user
$user = get_user($id);
// Check if user is admin
if ($user->is_admin) {
    // Allow access
    return true;
}
```
→ Refactoriza o borra los comentarios.

---

## JavaScript — patrón canónico

### Banner inicial (obligatorio si >100 LOC o archivo público)

```javascript
"use strict";
/**
 * <Modulo> — <Rol del archivo>
 *
 * <Descripción 1-3 líneas. Si es shared util, dilo. Si depende de
 * jQuery global, dilo.>
 */
(function () {
    // ...
})();
```

### Docblock de función exportada/pública

Conciso:

```javascript
/**
 * Build a CSRF-aware $.post payload by merging extra fields.
 */
function csrfData(extra) { ... }
```

### Separadores de sección en archivos largos

```javascript
// ── Initialization ──

function init() { ... }

// ── Event handlers ──

function onSubmit(e) { ... }

// ── Helpers ──

function escapeHtml(s) { ... }
```

### Anti-patrones JS

🔴 **Archivo de 200+ LOC con CERO comentarios**: imposible saber el rol del
módulo sin leer todo.

🔴 **JSDoc completo con `@param`/`@returns`/`@throws` en funciones de 3
líneas no exportadas**.

🔴 **Comentar cada línea de un `$.ajax`**.

---

## Lang (i18n) — patrón canónico

### Header del archivo

```php
<?php

defined('BASEPATH') or exit('No direct script access allowed');

/**
 * <Modulo> — English language strings
 *
 * @package <Modulo>
 */
```

### Separadores por sección (cada 8-12 keys aprox.)

```php
// ── Module name & permissions ──
$lang['<prefijo>'] = '<Modulo>';
$lang['<prefijo>_view_capability'] = 'View';
$lang['<prefijo>_manage_capability'] = 'Manage';

// ── Dashboard ──
$lang['<prefijo>_dashboard_title'] = '...';
$lang['<prefijo>_kpi_total'] = '...';

// ── Alerts & messages ──
$lang['<prefijo>_saved'] = 'Settings saved';
$lang['<prefijo>_error_invalid'] = 'Invalid input';
```

Mínimo en DupliGuard: 13 secciones en 247 líneas. Si tienes <5 secciones
en >150 líneas, falta estructura.

---

## Checklist rápida (para reviewer)

Al revisar un módulo, verifica:

### CSS
- [ ] ¿Cada archivo CSS tiene header con TOC?
- [ ] ¿Cada componente funcional (card, modal, hero, etc.) tiene separador
      `/* ═══ N. NOMBRE ═══ */` con 2-4 líneas de descripción?
- [ ] Ratio comentarios/LOC entre **8% y 12%**.
- [ ] Cero comentarios "por regla individual".
- [ ] Cero markers de "revert" sin descripción del componente.

### PHP
- [ ] ¿Header del archivo + docblock de clase concisos?
- [ ] ¿Métodos públicos con docblock de **1-2 líneas** (no 8+)?
- [ ] ¿Models con separadores `// ── Sección ──` agrupando métodos?
- [ ] Ratio entre **14% y 22%**. >25% = revisa si hay docblocks inflados.
- [ ] Comentarios inline explican POR QUÉ, no QUÉ.

### JS
- [ ] ¿Banner inicial en archivos >100 LOC?
- [ ] ¿Funciones públicas con docblock corto?
- [ ] ¿Separadores `// ── Sección ──` en archivos largos?
- [ ] Ratio entre **8% y 12%**. 0% = grave.

### Lang
- [ ] ¿Header del archivo?
- [ ] ¿Separadores `// ── Sección ──` cada 8-12 keys?
- [ ] Ratio entre **4% y 7%**.

---

## Cuándo aplicar este skill

- **perfex-module-builder**: al escribir CSS, PHP, JS, lang — sigue las
  plantillas literales arriba. Esto reemplaza la regla previa de "TABLE OF
  CONTENTS" suelta — ahora también obligatorio el separador por componente.
- **code-reviewer-pro**: añade a tu checklist el bloque "Comentarios y
  documentación" con la checklist rápida de arriba. Reporta hallazgos por
  archivo con ratio medido y patrón observado vs esperado.
- **codecanyon-qa**: NO bloquea por ratio (es calidad, no compliance), pero
  sí marca como "warning" si CSS tiene 0 separadores en archivos >500 LOC.

---

*Versión 1.0 — extraída del análisis cuantitativo DupliGuard vs brandkit_pro
(2026-05-11). Ratios medidos sobre 13 archivos representativos.*
