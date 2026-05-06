# Checklist de optimización segura

## Diagnóstico mínimo

- `uptime`
- `free -h`
- `df -hT`
- `ps -eo pid,ppid,comm,%cpu,%mem,rss --sort=-%mem | head -20`
- `journalctl --disk-usage`
- `docker system df`
- `docker stats --no-stream`

## Optimización segura sin tocar apps

Permitido, si corresponde:

- limpiar cache APT: `sudo apt-get clean`
- reducir tamaño de journal: `sudo journalctl --vacuum-time=7d`
- listar carpetas pesadas con `du` limitado
- listar imágenes/volúmenes/contenedores sin borrar
- listar logs grandes sin truncar

## Requiere aprobación

- borrar backups antiguos
- podar imágenes Docker
- truncar logs de contenedores
- cambiar `daemon.json`
- reiniciar Docker
- editar compose
- aplicar límites CPU/RAM
- reiniciar servicios
- deshabilitar servicios
- tocar DB
- reboot

## Política de recomendaciones

Toda recomendación debe incluir:

- beneficio esperado;
- riesgo;
- si requiere downtime;
- comando propuesto;
- rollback;
- prioridad: P1/P2/P3.

## Prioridades

P1:
- disco crítico >85%;
- RAM con swap constante;
- contenedor consumiendo recursos anómalos;
- logs o backups creciendo sin control.

P2:
- rotación de logs;
- límites de recursos en compose;
- retención de backups;
- revisión de servicios no usados.

P3:
- tuning kernel;
- tuning DB;
- cambio de arquitectura;
- separación de roles en VMs.
