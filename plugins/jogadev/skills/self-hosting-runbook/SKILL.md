---
name: self-hosting-runbook
description: Montar y operar servicios self-hosted en un home server detrás de un túnel saliente (Cloudflare Tunnel o similar). Úsalo al planear un homelab, exponer un servicio a internet sin abrir puertos, o desplegar apps/sitios en un servidor propio.
---

# Self-hosting runbook (plantilla)

Patrón para correr servicios propios 24/7 en un equipo de casa y exponerlos a internet **sin abrir puertos**, vía túnel saliente + reverse proxy. Reemplaza los `<PLACEHOLDERS>` por tus valores. **No guardes secretos en esta skill.**

## Componentes
- **Host**: equipo siempre encendido. Evita que se suspenda y haz que el runtime arranque al boot del SO.
- **Runtime**: Docker; contenedores con `--restart=always`.
- **Acceso admin**: VPN mesh con SSH por identidad (p.ej. Tailscale SSH). Nunca expongas SSH a internet.
- **Exposición pública**: túnel saliente (p.ej. Cloudflare Tunnel) → sin IP fija ni puertos abiertos; resuelve CGNAT/IP dinámica.
- **Dominio**: `<TU-DOMINIO>`, con DNS en el proveedor del túnel.

## Desplegar un servicio (patrón repetible)
1. **Contenedor**: corre la app publicando un **puerto host libre** `<PUERTO>`. Datos en disco rápido del host. Genera los secretos **en el host** (no en tu laptop) y guárdalos con permisos restringidos (`chmod 600`).
2. **DNS**: crea `<APP>.<TU-DOMINIO>` apuntando al túnel.
3. **Route/ingress**: enruta `<APP>.<TU-DOMINIO>` → `http://localhost:<PUERTO>` en la config del túnel o del reverse proxy.
4. **Aplica y verifica**: recarga el túnel; comprueba con `dig +short <APP>.<TU-DOMINIO>` y `curl -sI https://<APP>.<TU-DOMINIO>`.

## Según el tipo de servicio (paso 1)
- **Estático** (HTML/build): contenedor `nginx:alpine` sirviendo la carpeta del build (`-v <ruta>:/usr/share/nginx/html:ro`).
- **App con servidor** (Node/Next/Python/Go): su propio Dockerfile.
- **Con base de datos / varios servicios**: `docker compose` (app + DB en red propia; secretos por `.env` 600).

## Buenas prácticas
- **TLS en el borde** del túnel; el origen puede servir HTTP local.
- **Sync/streaming** (nube de archivos, media): exponlos **directo** por el túnel — los escudos anti-bot intermedios suelen romper WebDAV/streaming.
- **Resiliencia**: `--restart=always` + arranque del runtime al boot.
- **Progressive disclosure**: documenta el detalle operativo (inventario, troubleshooting) **en el propio server** y deja una skill local mínima que apunte allí.

## Seguridad mínima
- SSH admin solo por VPN de identidad, jamás puerto público.
- Cero secretos en texto plano versionado; genéralos en el host.
- Panel admin expuesto → auth fuerte o restríngelo a la VPN.
