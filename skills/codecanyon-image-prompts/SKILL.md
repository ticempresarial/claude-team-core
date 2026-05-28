---
name: codecanyon-image-prompts
description: Genera prompts optimizados para Midjourney / DALL-E / Imagen / Stable Diffusion que producen los 3 assets gráficos del listing CodeCanyon (Item Icon 80x80, Inline Preview 590x300, Hi-res Cover 2340x1560). NO genera las imágenes — solo los prompts. El usuario los pega en su herramienta IA favorita. Complementa al skill codecanyon-assets (medidas exactas) y al agente screenshot-capturer (capturas reales del producto, NO IA).
---

# Patrón canónico — Prompts IA para los 3 assets gráficos del listing CodeCanyon

> Este skill NO genera imágenes. Genera PROMPTS que el usuario pega en su
> herramienta favorita (Midjourney v6+, DALL-E 3, Google Imagen, Stable Diffusion).

## Qué hace este skill (y qué NO hace)

| ✅ Hace | ⛔ NO hace |
|---------|------------|
| Genera prompts optimizados para los 3 assets del LISTING | Generar las imágenes directamente |
| Adapta prompts al stack/categoría del producto | Subir las imágenes a CodeCanyon |
| Entrega 3 prompts (uno por asset) | Capturar screenshots del producto (eso es `screenshot-capturer`) |
| Reutiliza estilo visual consistente entre productos | Decidir paleta del producto (eso es `visual-director`) |

## Los 3 assets gráficos del listing (Envato)

| Asset | Dimensión exacta | Formato | Dónde aparece |
|-------|------------------|---------|----------------|
| **Item Icon** | 80x80 px | PNG transparente | Search results, sidebars, related items |
| **Inline Preview** | 590x300 px | PNG (puede transparente) | Header del listing arriba del título |
| **Hi-res Cover** | 2340x1560 px | PNG/JPG | Vista preview ampliada al hacer click en el cover |

⚠️ NO confundir con screenshots de la descripción (esos van adentro del HTML del listing
y son capturas REALES vía `screenshot-capturer`).

## Inputs que recibes

- `[PRODUCT_NAME]` — ej. "DupliGuard"
- `[PRODUCT_TAGLINE]` — ej. "Smart duplicate detection"
- `[STACK]` — Perfex / CI3 / Node / Laravel / WP
- `[CATEGORY]` — ej. "CRM Addon", "SaaS Platform", "Helpdesk"
- `[PRIMARY_COLOR]` — color principal del producto (de STYLE-GUIDE.md si existe)
- `[VISUAL_VIBE]` — ej. "professional + tech", "playful + modern", "enterprise + serious"
- `[DOMINANT_VISUAL_ELEMENT]` — ícono/símbolo que represente el producto (shield, chart, inbox, etc.)

## Output — los 3 prompts (formato standardizado)

```markdown
# Prompts CodeCanyon para [PRODUCT_NAME] v[VERSION]

## 1. Item Icon (80x80 px PNG transparente)

### Prompt para Midjourney v6+
```
[PROMPT_ICON_MJ]
--ar 1:1 --style raw --quality 1 --stylize 100
```

### Prompt para DALL-E 3
```
[PROMPT_ICON_DALLE]
Square aspect ratio, transparent background, 1024x1024 (downscale to 80x80 after).
```

### Prompt para Stable Diffusion
```
[PROMPT_ICON_SD]
Negative: text, watermark, signature, blurry, cluttered.
Sampler: DPM++ 2M Karras. Steps: 30. CFG: 7.
```

### Notas de post-proceso
- Downscale a 80x80 con Lanczos o bicúbico (Photoshop, Sharp, Pillow)
- Fondo transparente: si tu herramienta no lo soporta nativo, usar remove.bg
- Output: PNG con alpha channel
- Guardar como: `sales/listing-assets/01-item-icon-80x80.png`

---

## 2. Inline Preview (590x300 px PNG)

### Prompt para Midjourney v6+
```
[PROMPT_PREVIEW_MJ]
--ar 59:30 --style raw --quality 1 --stylize 150
```

### Prompt para DALL-E 3
```
[PROMPT_PREVIEW_DALLE]
Wide aspect ratio 16:9 approximately, suitable for header banner.
1792x1024 (crop to 590x300).
```

### Notas de post-proceso
- Output del Midjourney/DALL-E: 1456x768 aprox
- Crop a 590x300 manteniendo elementos clave centrados
- Composite (Photoshop/Figma): superponer 1 screenshot real del producto en
  la zona derecha (40% del ancho) para humanizar
- Guardar como: `sales/listing-assets/02-inline-preview-590x300.png`

---

## 3. Hi-res Cover (2340x1560 px PNG/JPG)

### Prompt para Midjourney v6+
```
[PROMPT_COVER_MJ]
--ar 39:26 --style raw --quality 2 --stylize 200 --v 6
```

### Prompt para DALL-E 3
```
[PROMPT_COVER_DALLE]
Large 3:2 aspect ratio suitable for hero cover. 1792x1024.
Upscale to 2340x1560 with AI upscaler (Topaz/Magnific) for production.
```

### Notas de post-proceso
- Output base: 1792x1024 o 2048x1536
- Upscale a 2340x1560 con: Topaz Gigapixel, Magnific.ai, o ESRGAN
- Composite con 2-3 screenshots del producto en mockup laptop/phone
- Texto overlay: "[PRODUCT_NAME]" + tagline (font: Inter Bold)
- Guardar como: `sales/listing-assets/03-hires-cover-2340x1560.jpg` (jpg si >2 MB en PNG)
```

## Plantillas de prompts por categoría de producto

El agente debe elegir la plantilla más cercana al stack/categoría del producto y
adaptar los placeholders.

### Plantilla A — CRM Addon (Perfex, Suite, etc.)

**Icon prompt base**:
```
Minimalist flat icon of [DOMINANT_VISUAL_ELEMENT], centered composition,
solid [PRIMARY_COLOR] color with subtle gradient, clean geometric shapes,
professional CRM software aesthetic, transparent background, modern UI
design language similar to Linear and Notion, no text, no watermark.
```

**Preview prompt base**:
```
Modern SaaS dashboard header banner showcasing [PRODUCT_NAME], featuring
abstract geometric shapes in [PRIMARY_COLOR] palette, clean professional
aesthetic suitable for B2B software, soft gradient background, hint of
data visualization elements (charts, graphs, tables), tech-forward design
inspired by Stripe Dashboard and Linear, wide aspect ratio, high quality.
```

**Cover prompt base**:
```
Hero cover image for [PRODUCT_NAME] - a [CATEGORY] - showing elegant
laptop mockup at angle displaying the product dashboard, surrounded by
abstract floating UI elements in [PRIMARY_COLOR] color scheme, professional
SaaS marketing aesthetic, soft studio lighting, subtle gradient background,
high-end product photography style, premium feel, no text overlay
(will be added in post-processing).
```

### Plantilla B — Standalone SaaS (Node, Laravel, CI3)

**Icon prompt base**:
```
Bold modern app icon for [PRODUCT_NAME], featuring [DOMINANT_VISUAL_ELEMENT]
in [PRIMARY_COLOR] gradient, rounded square container with soft shadow,
glassmorphism effect, premium tech aesthetic similar to Raycast and Linear,
clean professional look, transparent background, no text.
```

**Preview prompt base**:
```
Banner image for modern SaaS platform [PRODUCT_NAME], featuring abstract
visualization of [CATEGORY] workflow, [PRIMARY_COLOR] dominant palette
with white/dark accents, geometric shapes representing data flow,
contemporary tech design inspired by Vercel and Stripe, clean composition,
no text, suitable for marketing header.
```

**Cover prompt base**:
```
Hero shot of [PRODUCT_NAME] application interface displayed on multiple
device mockups (laptop, tablet, phone) floating in elegant arrangement,
[PRIMARY_COLOR] gradient background, soft studio lighting, professional
SaaS product photography style, premium marketing aesthetic, high detail,
no text overlay.
```

### Plantilla C — WordPress Plugin

**Icon prompt base**:
```
WordPress plugin icon for [PRODUCT_NAME], featuring [DOMINANT_VISUAL_ELEMENT]
in [PRIMARY_COLOR], rounded square frame, modern flat design with subtle
depth, WordPress brand-compatible aesthetic (works on both light and dark
themes), clean professional look, transparent background.
```

**Preview prompt base**:
```
Marketing banner for WordPress plugin [PRODUCT_NAME], showing WordPress
admin dashboard environment with [DOMINANT_VISUAL_ELEMENT] integration
highlighted, [PRIMARY_COLOR] accent color, professional plugin store
aesthetic, clean composition emphasizing the plugin's primary feature.
```

**Cover prompt base**:
```
Hero image for WordPress plugin [PRODUCT_NAME] featuring elegant laptop
mockup displaying WordPress admin with the plugin active, surrounded by
floating WP-style admin cards and the plugin's key UI elements, soft
[PRIMARY_COLOR] gradient background, professional plugin marketplace
aesthetic.
```

### Plantilla D — Helpdesk / Ticketing

**Icon prompt base**:
```
Modern flat icon representing customer support and helpdesk, featuring
chat bubble or ticket symbol in [PRIMARY_COLOR], clean geometric design,
professional B2B support tool aesthetic similar to Intercom and Zendesk,
transparent background, no text.
```

(continuar adaptando por categoría)

## Reglas duras para los prompts

### Generales
1. ✅ SIEMPRE incluir `--ar` ratio correcto para Midjourney
2. ✅ SIEMPRE pedir transparente para el icon (`transparent background`)
3. ✅ SIEMPRE incluir `no text, no watermark` (CodeCanyon agrega título aparte)
4. ✅ SIEMPRE mencionar referencias modernas (Linear, Vercel, Stripe, Notion, Raycast)
5. ⛔ NUNCA incluir personas reconocibles (problemas de derechos)
6. ⛔ NUNCA incluir logos de marcas reales (Apple, Google, etc.)
7. ⛔ NUNCA palabras como "screenshot" o "UI" (puede generar fake mockups)

### Para Midjourney
- Versión recomendada: v6 o v6.1
- `--style raw` para evitar el sesgo artístico exagerado
- `--quality 1` para Item Icon (rápido y suficiente)
- `--quality 2` para Hi-res Cover (más detalle)
- `--stylize` entre 100-200 (más bajo = más fiel al prompt)

### Para DALL-E 3
- Pedir 1024x1024 cuadrado para icon (downscale después)
- Pedir 1792x1024 para preview y cover (16:9 wide)
- DALL-E suele agregar texto no solicitado → pedir explícito "no text"

### Para Imagen 3 (Google)
- Mejor para fotorrealismo y mockups de productos
- Usar para Cover si quieres laptop mockup hiperreal

### Para Stable Diffusion (SDXL)
- Modelo recomendado: SDXL 1.0 o Juggernaut XL
- Sampler: DPM++ 2M Karras
- Steps: 30-40
- CFG: 6-8
- Negative prompt obligatorio: `text, watermark, signature, blurry, low quality, distorted, ugly, cluttered`

## Workflow del usuario después de recibir los prompts

```
1. Recibe los 3 prompts del agente
2. Abre su herramienta favorita (Midjourney via Discord, DALL-E via ChatGPT, etc.)
3. Pega prompt 1 (Item Icon) → genera → elige mejor variante → downscale a 80x80
4. Pega prompt 2 (Inline Preview) → genera → crop a 590x300 → composite con 1 screenshot real
5. Pega prompt 3 (Hi-res Cover) → genera → upscale a 2340x1560 → composite con 2-3 screenshots
6. Guarda los 3 PNG en sales/listing-assets/
7. Sube al formulario de CodeCanyon al hacer submit del item
```

## Estilo visual consistente entre productos

Para que tus productos en CodeCanyon se vean como "familia ticempresarial":

- **Paleta primaria recurrente**: define 2-3 colores que dominan en todos tus assets
  (sugerencia: deep blue #1f6feb + accent green #3fb950 + neutral gray #98a3b3)
- **Tipografía consistente**: usar Inter Bold para todos los text overlays
- **Mockup style consistente**: si usas laptop angle, úsalo en todos
- **Estética unificada**: si decides "Linear-inspired", aplícalo en todos

## Cuándo invocar este skill

- En el agente `codecanyon-release` después de tener screenshots reales listos
- Cuando el usuario pregunte "qué prompt uso para el icon/banner del producto"
- En la última fase de `/preparar-venta`

## Versionado

- v1.0 (2026-05-28): inicial. Plantillas por categoría + workflows por herramienta IA.
