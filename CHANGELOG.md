# Changelog

Todos los cambios notables a este plugin quedan registrados aquí.

El formato sigue [Keep a Changelog](https://keepachangelog.com/en/1.1.0/) y el
versionado sigue [Semantic Versioning](https://semver.org/).

## [Unreleased]

### Pendiente
- Crear repos hermanos `claude-team-{perfex,ci3,node,laravel,wp}`
- Configurar CI con GitHub Actions para validar formato
- Agregar carpeta `memory/` con lecciones aprendidas globales

## [0.2.0] - 2026-05-28

### Added — Primer poblado del equipo universal

**13 agents universales** (en `agents/`):
- `prompt-analyst` — refina el prompt/idea inicial del usuario
- `market-validator` — valida hueco de mercado en CodeCanyon
- `code-reviewer-pro` — calidad de ingeniería (DRY, complejidad)
- `universal-code-auditor` — audit calidad universal stack-agnostic
- `qa-gatekeeper` — gates entre sprints + único punto de contacto
- `visual-director` — dirección creativa, STYLE-GUIDE.md
- `ui-ux-designer` — accesibilidad, responsive, visual regression
- `database-architect` — schema, queries, índices
- `codecanyon-researcher` — investiga items publicados con chrome-devtools
- `security-auditor` — OWASP top 10
- `addy-code-reviewer` — Staff Engineer code review 5 ejes (Addy Osmani)
- `addy-security-auditor` — Security Engineer (Addy Osmani)
- `addy-test-engineer` — QA Engineer (Addy Osmani)

**27 skills universales** (en `skills/`):
- `universal-code-quality` — 15 dimensiones de calidad agnóstico de stack
- `codecanyon-assets` — specs gráficas Envato
- `demo-data-strategies` — patrones de seed multi-stack
- `comment-style-guide` — ratios de comentarios genéricos
- + los 23 skills de Addy Osmani (SDLC completo: spec → plan → build → verify → review → ship)

**14 slash commands** (en `commands/`):
- `/auditar-codigo` — universal audit (DEFAULT cuando no sabes el stack)
- `/investigar-competencia` — chrome-devtools MCP a CodeCanyon
- `/preparar-venta` — empaqueta ZIP final para CodeCanyon
- `/setup-mcp` — configura chrome-devtools project-scope
- `/check-mcp` — verifica estado de MCPs
- `/iniciar-equipo` — convoca al orquestador
- `/exportar-demo` — genera seed limpio para producción
- + los 7 commands de Addy: `/spec`, `/plan`, `/build`, `/test`, `/review`, `/code-simplify`, `/ship`

**Documentación**:
- `IMPROVING.md` — guía completa de mejora continua del equipo (12 secciones)

## [0.1.0] - 2026-05-28

### Added
- Estructura inicial del repo
- `.claude-plugin/plugin.json` con metadata
- README con instrucciones de instalación (vía `/plugin` y manual)
- CHANGELOG siguiendo Keep a Changelog
- Carpetas placeholder: `agents/`, `skills/`, `commands/`, `memory/`
