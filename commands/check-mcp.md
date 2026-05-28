---
description: Verifica el estado de TODOS los MCPs configurados (Google Drive, chrome-devtools, dart, flutter-inspector). Reporta cuáles están conectados, cuáles caídos, y sugiere acciones para reconectar. Ejecutar antes de cualquier trabajo de QA visual o testing Flutter.
---

Vas a verificar la salud de los MCP servers configurados.

## Pasos

### 1. Ejecutar claude mcp list

```powershell
claude mcp list
```

Captura el output. Para cada MCP, identifica:
- ✓ Connected
- ✗ Disconnected
- ⚠️ Failed to connect

### 2. Verificar procesos físicos (cuando aplique)

Para `chrome-devtools`, valida que pueda lanzarse:

```powershell
Get-CimInstance Win32_Process -Filter "Name='node.exe'" |
  Where-Object { $_.CommandLine -like '*chrome-devtools-mcp*' } |
  Select-Object ProcessId, CommandLine
```

Para `flutter-inspector`, valida que el binario exista:

```powershell
Test-Path "$env:USERPROFILE\mcp_flutter\mcp_server_dart\build\flutter-inspector-server.exe"
```

### 3. Verificar project-scope local (si aplica)

```powershell
$settingsPath = "$PWD\.claude\settings.local.json"
if (Test-Path $settingsPath) {
    $local = Get-Content $settingsPath -Raw | ConvertFrom-Json
    if ($local.mcpServers."chrome-devtools") {
        Write-Host "✅ MCP project-scope chrome-devtools registrado para este proyecto"
    } else {
        Write-Host "⚠️ Este proyecto no tiene MCP project-scope. Ejecuta /setup-mcp"
    }
} else {
    Write-Host "⚠️ Este proyecto no tiene .claude/settings.local.json. Ejecuta /setup-mcp"
}
```

### 4. Reporte al usuario

```markdown
## 🔍 Estado MCPs — <cwd actual>

### Servers configurados
| MCP | Estado | Scope |
|-----|--------|-------|
| claude.ai Google Drive | ✓ Connected | user |
| chrome-devtools | ✓ Connected / ✗ Disconnected | user + project (si aplica) |
| dart | ✓ Connected | user |
| flutter-inspector | ✓ Connected / ✗ Disconnected | user |

### Project-scope override
- ✅ Este proyecto tiene MCP chrome-devtools propio (profile en `<cwd>\.claude\chrome-profile`)
- ⚠️ Este proyecto NO tiene MCP project-scope — usa global con --isolated
- ❌ Este proyecto NO tiene .claude/settings.local.json — ejecuta /setup-mcp

### Procesos vivos detectados
- chrome-devtools-mcp: <N> proceso(s) corriendo
- flutter-inspector binario: ✅ existe / ❌ falta recompilar

### Veredicto
🟢 Todo OK — puedes trabajar
🟡 Faltan project-scope MCPs — ejecuta /setup-mcp
🔴 Hay MCPs caídos — sigue acciones abajo

### Si algún MCP está caído

Acción 1 (la más efectiva): **Recarga la ventana de Claude Code**
- En VSCode: `Ctrl+Shift+P` → "Developer: Reload Window"
- O cierra el hilo de Claude Code y reábrelo
- Eso fuerza al harness a re-spawnear los MCP servers

Acción 2 (si el problema persiste): Mata procesos huérfanos
```powershell
Get-WmiObject Win32_Process -Filter "Name='node.exe'" |
  Where-Object { $_.CommandLine -like '*chrome-devtools-mcp*' } |
  ForEach-Object { Stop-Process -Id $_.ProcessId -Force }

Remove-Item -Path "$env:USERPROFILE\.cache\chrome-devtools-mcp\chrome-profile\lockfile" -Force -ErrorAction SilentlyContinue
```

Después de matar, recarga ventana y reintenta.

Acción 3: Re-registra el MCP
- Si es user-scope: `claude mcp remove chrome-devtools --scope user; claude mcp add --scope user chrome-devtools -- npx -y chrome-devtools-mcp@latest --isolated`
- Si es project-scope: ejecuta `/setup-mcp`
```

## Reglas

- NO modifiques nada automáticamente. Solo diagnostica y sugiere.
- Si el usuario quiere que repares, te lo pide explícito.
- Si detectas que está dentro de un proyecto sin project-scope, sugiere
  `/setup-mcp` como acción recomendada.
