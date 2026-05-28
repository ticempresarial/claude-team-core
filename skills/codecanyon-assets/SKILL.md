---
name: codecanyon-assets
description: Especificaciones exactas y prompts de IA para generar assets gráficos de un listing CodeCanyon (icono 80x80, banner 590x300, hi-res cover 2340x1560, screenshots 1920x1080). Activa cuando un agente esté preparando material visual de un producto o el usuario pregunte por medidas/prompts de assets.
---

# Assets gráficos para CodeCanyon — specs y prompts

Reglas exactas según documentación Envato + lecciones de productos aprobados
(DupliGuard) y rechazados (ChatBridge, OmniInbox).

---

## 1. Item Icon (obligatorio)

| Spec | Valor |
|---|---|
| Resolución | **80×80 px** |
| Formato | PNG con transparencia |
| Aspect ratio | 1:1 |
| Safe-area | 10% padding interno (no llenar borde a borde) |
| Estilo | Flat / minimalista |

**Plantilla de prompt para IA**:

```
Create a flat minimalist icon, 80x80 pixels, transparent PNG.

Subject: <símbolo concreto del módulo, ej. "two overlapping document outlines with a magnifying glass" para detector de duplicados>

Style:
- Flat design, no gradients beyond a single subtle highlight
- 2-3 color palette maximum
- Primary color: <hex Perfex blue #2196F3 o coherente con tu producto>
- 10% safe-area padding from edges
- Crisp lines, readable at 32x32 thumbnail

Output: 80×80 PNG with transparent background, no text, no border.
```

---

## 2. Inline Preview (obligatorio)

| Spec | Valor |
|---|---|
| Resolución | **590×300 px** |
| Formato | PNG |
| Uso | Banner que se ve en el listing |

**Plantilla de prompt**:

```
Create a 590x300 pixel banner image for a CodeCanyon product listing.

Product: <Nombre>
Tagline: <1 frase de propuesta de valor en menos de 10 palabras>

Style:
- Hero composition with product name as headline
- Subtle Perfex-related visual cue (CRM dashboard hint, abstract data flow, etc.)
- Background gradient or clean solid (not busy)
- Coherent palette with the 80x80 icon
- Modern sans-serif typography
- Leave breathing room — don't crowd

Output: 590×300 PNG, no overlapping watermarks, no logos other than product name.
```

---

## 3. Hi-res Cover (recomendado)

| Spec | Valor |
|---|---|
| Resolución | **2340×1560 px** (3:2) |
| Formato | PNG |
| Uso | Hero principal del listing en pantallas grandes |

**Plantilla de prompt**:

```
Create a high-resolution cover image, 2340x1560 pixels (3:2), for a CodeCanyon product.

Product: <Nombre>
Concept: <una idea visual concreta — ej. "split-screen showing duplicate records on left being merged into one clean record on right">

Style:
- Editorial / hero composition
- Soft depth (not flat) — subtle shadows, light source from top-left
- Mockup framing acceptable (browser chrome, dashboard preview)
- Tagline overlay: "<one-liner>" in clean typography
- Same color palette as icon and inline preview

Output: 2340×1560 PNG, sharp, suitable for printing / large display.
```

---

## 4. Screenshots (obligatorios — 8 a 14)

| Tipo | Resolución | Formato |
|---|---|---|
| Desktop / admin | **1920×1080 px** | PNG |
| Mobile (si aplica) | **1080×1920 px** | PNG |
| Tamaño máx por imagen | 500 KB | comprimir con TinyPNG / ImageOptim |

**Naming**: `01-login.png`, `02-dashboard.png`, `03-list.png`, `04-detail.png`,
`05-form.png`, `06-settings.png`, ...

**Captura con Chrome DevTools MCP** (si demo está corriendo):

```
1. mcp__chrome-devtools__new_page → URL del demo
2. mcp__chrome-devtools__resize_page → 1920x1080
3. Login con credenciales demo
4. Navegar a cada vista del módulo
5. mcp__chrome-devtools__take_screenshot → guardar como 0X-<descripción>.png
6. Optimizar (TinyPNG opcional)
```

**Qué capturar (orden recomendado)**:

1. **Dashboard del módulo** (vista principal — primera impresión)
2. **Lista de elementos** con datos demo realistas (no Lorem Ipsum)
3. **Vista de detalle / formulario** mostrando capacidad
4. **Acción destacada** (lo que más diferencia tu producto)
5. **Settings del módulo**
6. **Vista mobile** de la principal (si tu producto tiene UI mobile)
7. **Estado vacío** bien diseñado (empty state)
8. **Confirmación de éxito** (si tu UI tiene flows multi-paso)

---

## 5. Submission packaging — verificación final

Antes de subir, verifica:

- [ ] icono.png — 80×80, transparente, ≤50 KB
- [ ] banner.png — 590×300, ≤200 KB
- [ ] cover-2340x1560.png — 2340×1560, ≤500 KB
- [ ] 8-14 screenshots numerados, cada uno ≤500 KB
- [ ] Naming consistente (todos lowercase, sin espacios)
- [ ] Sin watermarks ajenos (Adobe Stock, etc.)
- [ ] Sin información sensible visible (emails reales, llaves API, datos privados)

---

## 6. Errores comunes que rechazan

- ❌ Icono que se ve mal a 32×32 (líneas finas que se pierden)
- ❌ Banner con texto ilegible en pantallas pequeñas
- ❌ Screenshots con UI rota / errores 404 / placeholders sin datos
- ❌ Pegar la misma vista 3 veces con detalles distintos (Envato lo nota)
- ❌ Screenshots de un producto distinto (mockups copiados)
- ❌ Pesos > 500 KB sin comprimir (rechazo automático)
