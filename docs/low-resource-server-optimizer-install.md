# Instalación en el repo mateoschreiber/codex-plugins

## 1. Copiar archivos

Extraer el ZIP en la raíz del repo:

```bash
cd codex-plugins
unzip low-resource-server-optimizer-plugin.zip
```

Debe quedar:

```text
plugins/low-resource-server-optimizer/
```

## 2. Agregar entrada al marketplace

Abrir:

```bash
nano .agents/plugins/marketplace.json
```

Dentro del array `plugins`, agregar esta entrada:

```json
{
  "name": "low-resource-server-optimizer",
  "source": {
    "source": "local",
    "path": "./plugins/low-resource-server-optimizer"
  },
  "policy": {
    "installation": "AVAILABLE",
    "authentication": "ON_INSTALL"
  },
  "category": "Productivity"
}
```

Si ya hay una entrada antes, recordar separar objetos con coma.

## 3. Subir a GitHub

```bash
git add .
git commit -m "Add low resource server optimizer Codex plugin"
git push
```

## 4. Actualizar marketplace en equipos donde ya está instalado

```bash
codex plugin marketplace upgrade mateo-codex-plugins
```

O actualizar todos:

```bash
codex plugin marketplace upgrade
```

## 5. Instalar desde Codex

Abrir Codex:

```bash
codex
```

Luego:

```text
/plugins
```

Instalar:

```text
low-resource-server-optimizer
```

Verificar:

```text
/skills
```

Usar:

```text
$low-resource-server-optimizer Optimiza este servidor de bajos recursos sin detener ni modificar apps corriendo.
```
