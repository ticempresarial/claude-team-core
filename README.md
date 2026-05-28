# claude-team-core

Equipo IA universal de **ticempresarial** — base común para todos los proyectos.

Vive en GitHub: <https://github.com/ticempresarial/claude-team-core>

## Qué incluye

Este es el **plugin base** que se carga en todos los proyectos de la agencia,
independiente del stack tecnológico. Aplica criterios de calidad universales,
sin sesgar por elección de framework.

```
claude-team-core/
├── .claude-plugin/
│   └── plugin.json        # metadata del plugin
├── agents/                # agentes stack-agnostic
├── skills/                # skills universales
├── commands/              # slash commands de uso transversal
├── memory/                # referencias y lecciones de uso global
└── README.md
```

## Stacks complementarios

Para trabajo específico por stack, este plugin se combina con uno de:

- `claude-team-perfex` — módulos Perfex CRM
- `claude-team-ci3` — productos CI3 standalone
- `claude-team-node` — productos Node.js / Next.js
- `claude-team-laravel` — productos Laravel SaaS
- `claude-team-wp` — plugins WordPress

## Instalación

### Vía Claude Code `/plugin` (recomendado)

```
/plugin marketplace add ticempresarial/claude-team-core
/plugin install claude-team-core@ticempresarial/claude-team-core
```

### Manual (si `/plugin` no está disponible)

```bash
git clone https://github.com/ticempresarial/claude-team-core ~/.claude/plugins/marketplaces/claude-team-core
```

## Uso en un proyecto

Cada proyecto declara qué plugins activar en `.claude/settings.json`:

```json
{
  "enabledPlugins": [
    "claude-team-core",
    "claude-team-perfex"
  ]
}
```

## Versionado

Sigue [SemVer](https://semver.org/). Cada release tiene tag `vX.Y.Z`.

- `0.x` — en construcción
- `1.0.0` — primer release estable

## Licencia

MIT — ver [LICENSE](./LICENSE).
