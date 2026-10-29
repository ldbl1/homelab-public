# Homelab Docker

Repositorio con las plantillas de `docker-compose` que utilizo en mi red local. He limpiado los ficheros de claves, tokens e IPs privadas para tener una copia pública y reutilizable de la configuración de cada contenedor.

El repositorio está dividido en dos carpetas principales según la máquina donde corre cada servicio (`serverlorodo` y `pilorodo`). Dentro de cada nodo, cada servicio va en su propia carpeta con su `docker-compose.yml` y un `.env.example` con las variables que necesita para arrancar.

## Estructura del repositorio

### 1. `serverlorodo/` (Servidor principal)
Contiene el grueso de aplicaciones, bases de datos y servicios multimedia:

* **beszel**: Panel central de monitorización de recursos y estado de contenedores.
* **bookstack**: Wiki local para apuntes y documentación técnica.
* **calibreweb**: Biblioteca de libros electrónicos.
* **downtify**: Descarga de música.
* **homebox**: Inventario de cosas de casa y equipamiento.
* **homepage**: Panel principal con accesos directos a todos los servicios de la red.
* **immich-v3**: Copia de seguridad y galería de fotos y vídeos.
* **jellyfin**: Servidor multimedia para pelis y series.
* **kuma**: Uptime Kuma para vigilar que los contenedores y servicios no se caigan.
* **n8n**: Automatizaciones y flujos de trabajo.
* **navidrome**: Servidor de música en streaming.
* **nextcloud**: Nube privada para archivos.
* **nginx-proxy-manager**: Proxy inverso para gestionar dominios locales y certificados SSL.
* **obsidian-livesync**: Base de datos CouchDB para sincronizar las notas de Obsidian entre dispositivos.
* **paperless**: Gestión de documentos y facturas escaneadas con OCR.
* **romm**: Gestor de biblioteca de ROMs y juegos retro.
* **thingport**: Gestión y seguimiento de inventario/activos.

*(Nota: Algunos contenedores como Webtop los he dejado fuera de este repo a propósito).*

### 2. `pilorodo/` (Raspberry Pi - Nodo secundario)
Servicios ligeros de red, DNS y agentes de monitorización que corren en la Raspberry Pi de forma independiente al servidor principal:

* **beszel-agent**: Agente ligero para enviar métricas de la Raspberry Pi al servidor de Beszel.
* **glances**: Monitor de recursos del sistema en tiempo real (usado también para alimentar widgets en Homepage).
* **hawser**: Agente remoto para gestión y monitorización de contenedores Docker.
* **pi-hole**: Servidor DNS local y bloqueador de publicidad para toda la red.
* **watchtower**: Actualización automática de imágenes y contenedores Docker.

## Red y acceso remoto

Los contenedores no están expuestos directamente a internet ni hay puertos abiertos en el router:

* **En red local**: Las peticiones pasan por **Pi-hole** (en `pilorodo`) como servidor DNS local y **Nginx Proxy Manager** (en `serverlorodo`) se encarga de enrutar cada subdominio a su contenedor correspondiente con su certificado SSL.
* **Desde fuera de casa**: Todo el acceso remoto se hace a través de **Tailscale** instalado en los nodos. Al conectarse a la VPN desde el móvil o desde otro equipo fuera de casa, se accede a todos los servicios exactamente igual que estando en la red local sin exponer nada hacia fuera.

## Cómo desplegar un servicio

Para levantar cualquiera de los contenedores desde cero:

1. Entra en la carpeta del nodo (`serverlorodo` o `pilorodo`) y luego en la del servicio que quieras levantar. Si incluye archivo de variables de ejemplo, haz una copia:
   ```bash
   cp .env.example .env
Edita el .env y el docker-compose.yml para ajustar tus datos:

Cambia todos los valores CHANGE_ME por tus contraseñas o claves reales.

Revisa las rutas de los volúmenes por si tu estructura de carpetas o discos es distinta.

En los contenedores que llevan configuración de red o DNS (como n8n, bookstack, homepage, kuma o pi-hole), cambia las IPs de ejemplo (192.168.1.100 y 192.168.1.2) por las IPs reales de tus máquinas en la red local.

Comprueba que el compose no tenga fallos de sintaxis y levántalo:

   ```bash
   docker compose config --quiet
   docker compose pull
   docker compose up -d
   ```

Notas para mantener el repo
No subir nunca los archivos .env reales, bases de datos (*.db, *.sqlite), certificados ni copias de seguridad.

Cualquier contraseña o token nuevo que se añada a un docker-compose.yml tiene que ir referenciado como variable con valor por defecto ${VARIABLE:-CHANGE_ME} para no volcar credenciales por error al actualizar este repositorio.
