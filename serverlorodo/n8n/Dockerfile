# 1. Usamos una imagen Alpine limpia para extraer el gestor de paquetes estático
FROM alpine:latest AS builder
RUN apk add --no-cache apk-tools-static

# 2. Usamos la imagen oficial de n8n desde Docker Hub
FROM n8nio/n8n:latest

USER root

# 3. añado el gestor de paquetes desde nuestra imagen limpia
COPY --from=builder /sbin/apk.static /sbin/apk
COPY --from=builder /etc/apk/repositories /etc/apk/repositories

# 4. Instalamos exiftool (para LEER) y py3-mutagen (para ESCRIBIR en MP3)
RUN apk add --no-cache exiftool py3-mutagen

USER node
