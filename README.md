# Codex Plugins

Marketplace privado de plugins/skills para Codex.

## Plugin incluido

### docker-compose-app-install

Skill para instalar nuevas aplicaciones self-hosted dentro de un Docker Compose existente de forma segura.

Incluye reglas para:

- backup previo
- no usar `docker compose down` global
- no tocar servicios no relacionados
- validar con `docker compose config`
- usar volúmenes persistentes
- no exponer bases de datos
- detectar reverse proxy existente
- levantar solo servicios nuevos
- entregar rollback

## Instalación en Codex

### Repo público

```bash
codex plugin marketplace add TU_USUARIO/codex-plugins --ref main
```

### Repo privado usando SSH

```bash
codex plugin marketplace add git@github.com:TU_USUARIO/codex-plugins.git --ref main
```

Luego abrir Codex:

```bash
codex
```

Dentro de Codex:

```text
/plugins
```

Instalar:

```text
docker-compose-app-install
```

Usar:

```text
$docker-compose-app-install Instala Planka en este compose existente. No tocar otros servicios.
```
