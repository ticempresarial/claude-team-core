---
description: Configura el MCP chrome-devtools project-scope para el proyecto del cwd actual con userDataDir único. Aisla este proyecto de otros hilos paralelos. Idempotente — si ya existe, no duplica. Ejecutar UNA VEZ al entrar a un proyecto preexistente que no haya pasado por /nuevo-modulo-perfex.
---

Vas a configurar el MCP `chrome-devtools` con scope `project` para el
cwd actual. Eso asegura que este proyecto tenga su propio profile de
Chrome aislado de otras sesiones paralelas.

## Pasos

### 1. Detectar cwd y validar que es un proyecto

```powershell
$cwd = $PWD.Path
Write-Host "Configurando MCP project-scope para: $cwd"
```

Si el cwd es la raíz del usuario o un disco vacío, **ABORTA** con
mensaje "Este comando se ejecuta dentro de la carpeta de un proyecto,
no a nivel global. Cambia al cwd del módulo y reintenta."

### 2. Verificar si ya está configurado

```powershell
$settingsPath = "$cwd\.claude\settings.local.json"
if (Test-Path $settingsPath) {
    $existing = Get-Content $settingsPath -Raw | ConvertFrom-Json
    if ($existing.mcpServers."chrome-devtools") {
        Write-Host "Ya está configurado. Skipping."
        exit 0
    }
}
```

Si ya existe el MCP `chrome-devtools` en project-scope, reporta y sale.

### 3. Crear directorio del profile + registrar MCP

**IMPORTANTE — bypass de npx**: el wrapper `npx -y` produce buffer mal
sincronizado con el handshake MCP en Windows. Apunta directo a `node.exe`
+ script absoluto del paquete instalado globalmente.

Pre-requisito: `chrome-devtools-mcp` debe estar instalado globalmente.
Verifica:
```powershell
Test-Path "$env:APPDATA\npm\node_modules\chrome-devtools-mcp\build\src\bin\chrome-devtools-mcp.js"
```

Si retorna `False`, instala primero:
```powershell
npm install -g chrome-devtools-mcp@latest
```

Registro:
```powershell
$profileDir = "$cwd\.claude\chrome-profile"
New-Item -Path $profileDir -ItemType Directory -Force | Out-Null

$nodeExe = "C:\Program Files\nodejs\node.exe"
$scriptPath = "$env:APPDATA\npm\node_modules\chrome-devtools-mcp\build\src\bin\chrome-devtools-mcp.js"

claude mcp add --scope project chrome-devtools -- "$nodeExe" "$scriptPath" --userDataDir "$profileDir"
```

### 4. Verificar instalación

```powershell
claude mcp list
```

Debe mostrar `chrome-devtools` ✓ Connected.

Si la lista NO incluye el chrome-devtools project-scope o el status es
desconectado, sugiere al usuario:

```
⚠️ El MCP se registró pero no aparece conectado todavía.
Acciones:
1. Recarga la ventana de Claude Code: Ctrl+Shift+P → "Reload Window"
2. O cierra/abre el hilo de Claude Code
3. Tras reload, vuelve a ejecutar /check-mcp para validar
```

### 5. Reporte al usuario

```markdown
## ✅ MCP project-scope configurado para <nombre-proyecto>

### Lo que hice
- Creado: `<cwd>\.claude\chrome-profile\` (profile aislado para este proyecto)
- Registrado: `chrome-devtools` con scope `project` en `<cwd>\.claude\settings.local.json`
- userDataDir único: `<cwd>\.claude\chrome-profile`

### Por qué importa
Cualquier QA visual con `chrome-devtools` en este hilo usa SU profile,
sin tocar el de los otros 3 hilos paralelos que tengas abiertos.

### Cómo confirmar que está vivo
Ejecuta: **`/check-mcp`**

### Si no aparece conectado
Recarga la ventana de Claude Code (`Ctrl+Shift+P → Reload Window`) y
re-ejecuta `/check-mcp`.
```

## Reglas

- NO modifiques `~/.claude.json` (user scope). Solo el project scope.
- NO toques los MCPs de otros proyectos.
- Si el cwd actual NO es una carpeta de proyecto identificable (es root
  de disco, o `Documents`, etc.), aborta con mensaje claro.
- Idempotente: si ya está configurado, reporta y sale sin error.
