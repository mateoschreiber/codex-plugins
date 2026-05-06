---
name: low-resource-server-optimizer
description: Optimizar servidor Linux/Docker de bajos recursos sin detener, eliminar ni modificar apps corriendo. Usar para pedidos de optimización de CPU, RAM, disco, logs, Docker, systemd, backups y servicios en servidores productivos o homelab, priorizando acciones seguras y economía de tokens.
---

# Low Resource Server Optimizer

Usar este skill cuando la tarea sea **optimizar un servidor de bajos recursos** sin afectar aplicaciones, contenedores, bases de datos ni servicios actualmente en ejecución.

## Objetivo

Maximizar rendimiento y liberar recursos de forma segura, medible y reversible, con mínima salida de texto.

## Reglas absolutas

- No detener, reiniciar, recrear, eliminar ni modificar apps, servicios o contenedores en ejecución.
- No ejecutar `docker compose down`.
- No ejecutar `docker system prune`, `docker volume prune`, `docker container prune` ni limpieza destructiva.
- No eliminar volúmenes, bases de datos, backups, configuraciones, uploads ni datos de usuario.
- No modificar `docker-compose.yml`, `.env`, Nginx/Traefik/Caddy, systemd, cron, firewall, kernel params ni daemon Docker sin aprobación explícita.
- No instalar paquetes nuevos salvo aprobación explícita.
- No exponer secretos, tokens, passwords ni dumps.
- No aplicar cambios que requieran reboot o restart de servicios sin aprobación explícita y ventana de mantenimiento.
- Si una acción puede afectar disponibilidad, proponerla como "pendiente con aprobación", no ejecutarla.

## Principio de trabajo

Primero medir. Luego optimizar solo lo seguro. Finalmente comparar antes/después.

## Modo de salida

Responder siempre en español, técnico y breve.

No volcar logs completos. Limitar evidencia a lo relevante.

Formato final obligatorio:

```text
Resumen:
- Estado inicial:
- Acciones seguras aplicadas:
- Acciones NO aplicadas por requerir aprobación:
- Estado final:
- Riesgos:
- Rollback:
```

## Flujo obligatorio

### 1. Identificar entorno

Ejecutar solo comandos de lectura:

```bash
hostnamectl || true
uptime
free -h
df -hT
lsblk -f
docker --version 2>/dev/null || true
docker compose version 2>/dev/null || true
```

Detectar si es:

- Ubuntu/Debian.
- Docker host.
- Docker Compose host.
- VM de bajos recursos.
- Servidor con servicios systemd.
- Servidor con Zabbix/NetBox/Nextcloud/Portainer/Jellyfin/OpenProject/Planka u otros.

### 2. Medir consumo actual

Ejecutar lectura compacta:

```bash
printf "\nCPU/RAM TOP:\n"
ps -eo pid,ppid,comm,%cpu,%mem,rss --sort=-%mem | head -20

printf "\nDISCO:\n"
df -hT

printf "\nJOURNAL:\n"
journalctl --disk-usage 2>/dev/null || true

printf "\nDOCKER USO:\n"
docker system df 2>/dev/null || true

printf "\nCONTENEDORES:\n"
docker stats --no-stream --format "table {{.Name}}\t{{.CPUPerc}}\t{{.MemUsage}}\t{{.NetIO}}\t{{.BlockIO}}" 2>/dev/null || true
```

No ejecutar escaneos pesados de disco sobre `/` completo salvo que sea necesario. Si se requiere, usar profundidad limitada y evitar mounts remotos:

```bash
du -xh --max-depth=1 / 2>/dev/null | sort -h | tail -20
```

### 3. Clasificar problemas

Clasificar en:

- CPU alta.
- RAM baja/swap.
- Disco lleno.
- Logs excesivos.
- Backups excesivos.
- Imágenes Docker no usadas.
- Contenedores con alto consumo.
- Servicios systemd no relacionados.
- DB pesada.
- I/O saturado.

### 4. Acciones seguras permitidas sin aprobación adicional

Estas acciones son permitidas si corresponden y no afectan apps:

```bash
sudo apt-get clean
sudo journalctl --vacuum-time=7d
```

También se permite generar reportes de tamaño:

```bash
docker system df
journalctl --disk-usage
du -xh --max-depth=1 /var/log 2>/dev/null | sort -h | tail -20
du -xh --max-depth=1 /opt 2>/dev/null | sort -h | tail -20
```

Si no hay `sudo` o falla un permiso, reportar sin insistir.

### 5. Acciones que requieren aprobación explícita

No ejecutar sin confirmar:

- `docker image prune`.
- `docker builder prune`.
- truncar logs JSON de contenedores.
- modificar `/etc/docker/daemon.json`.
- reiniciar Docker.
- reiniciar contenedores.
- modificar límites de CPU/RAM en compose.
- modificar políticas de backup.
- borrar backups antiguos.
- borrar archivos grandes.
- `apt autoremove`.
- deshabilitar servicios.
- cambiar swappiness/cache pressure.
- comprimir/mover datos.
- tocar bases de datos.
- ejecutar vacuum/optimize de DB.
- reiniciar MySQL/PostgreSQL/MariaDB.
- cambiar cron jobs.
- reboot.

### 6. Docker: análisis no destructivo

Si Docker existe:

```bash
docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"
docker system df
docker images --format "table {{.Repository}}\t{{.Tag}}\t{{.Size}}\t{{.CreatedSince}}" | head -50
docker volume ls
```

No borrar nada. Proponer limpieza solo si se detecta ahorro claro.

### 7. Logs Docker

Si `/var/lib/docker/containers` existe, medir:

```bash
sudo find /var/lib/docker/containers -name "*-json.log" -printf "%s %p\n" 2>/dev/null | sort -n | tail -20
```

No truncar logs sin aprobación. Si logs son el problema, proponer:

- rotación por `daemon.json`.
- truncado puntual con backup/confirmación.
- reinicio programado de Docker si corresponde.

### 8. Backups

Detectar carpetas probables sin borrar:

```bash
find /srv /opt /var/backups /home -maxdepth 3 -type d \( -iname "*backup*" -o -iname "*backups*" \) 2>/dev/null
```

No borrar backups. Proponer política de retención concreta si hay exceso.

### 9. Salida recomendada

Usar máximo:

- 1 resumen.
- 1 tabla compacta.
- 1 lista de acciones aplicadas.
- 1 lista de acciones pendientes con aprobación.
- 1 bloque de rollback.

No incluir explicaciones largas.

### 10. Rollback

Para cada cambio aplicado, indicar rollback.

Ejemplos:

- `apt-get clean`: no requiere rollback.
- `journalctl --vacuum-time=7d`: logs antiguos eliminados, no recuperables salvo backup/snapshot.
- cambios de configuración: restaurar backup creado antes del cambio.

## Criterio de éxito

La tarea está completa solo si entrega:

- métricas iniciales;
- acciones aplicadas sin afectar apps;
- recursos liberados estimados;
- acciones recomendadas pendientes;
- riesgos;
- rollback;
- próximos comandos seguros.

## Referencia extendida

Si necesitás más detalle operativo, consultar `references/optimization-checklist.md`.
