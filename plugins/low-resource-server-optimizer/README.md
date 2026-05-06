# Low Resource Server Optimizer

Plugin/skill para Codex orientado a optimizar servidores Linux/Docker de bajos recursos sin tocar aplicaciones en ejecución.

## Uso

```text
$low-resource-server-optimizer Optimiza este servidor de bajos recursos sin detener ni modificar apps corriendo.
```

## Qué hace

- mide CPU, RAM, disco, Docker, logs y servicios;
- identifica cuellos de botella;
- aplica solo acciones seguras;
- propone acciones riesgosas como pendientes con aprobación;
- entrega resumen corto con antes/después y rollback.

## Qué no hace

- no reinicia servicios;
- no borra apps;
- no elimina volúmenes;
- no toca bases de datos;
- no modifica compose;
- no ejecuta prune destructivo;
- no cambia firewall/proxy/systemd sin aprobación.
