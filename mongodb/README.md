# MongoDB de desarrollo

## Configuración interactiva

Puedes crear `.env` y los Docker secrets sin preparar manualmente los archivos:

```bash
bash setup.sh
```

El script solicita el usuario y la contraseña root, la versión de MongoDB, el puerto,
el nombre del contenedor, el volumen, el proyecto Compose y la base de datos. La
contraseña se solicita sin mostrarla en la consola. Al final permite validar la
configuración y arrancar el contenedor.

También puedes consultar la ayuda:

```bash
bash setup.sh --help
```

## Arranque

```powershell
Copy-Item .env.example .env
docker compose --env-file .env up -d
```

Los secretos locales se encuentran en `secrets/` y no se versionan. Para otro entorno,
copia las plantillas `.example` y sustituye sus valores antes de arrancar el contenedor.

## Opciones configurables

Edita `.env` para cambiar:

- `COMPOSE_PROJECT_NAME`: nombre del proyecto Compose y de su red.
- `MONGO_CONTAINER_NAME`: nombre del contenedor.
- `MONGO_IMAGE_TAG`: versión de la imagen de MongoDB.
- `MONGO_HOST_PORT`: puerto publicado en el host.
- `MONGO_VOLUME_NAME`: volumen donde se conserva `/data/db`.
- `MONGO_DATABASE`: base inicial usada por la imagen al inicializar el volumen.

Para crear otra instancia en paralelo, usa otro `.env`, cambia al menos
`COMPOSE_PROJECT_NAME`, `MONGO_CONTAINER_NAME`, `MONGO_HOST_PORT` y
`MONGO_VOLUME_NAME`, y arranca el mismo compose con ese archivo.