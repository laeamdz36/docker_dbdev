# MongoDB de desarrollo

## Configuración interactiva

Puedes crear `.env` y los Docker secrets sin preparar manualmente los archivos:

```bash
bash setup.sh
```

El script solicita el usuario y la contraseña root, la versión de MongoDB, el puerto,
el nombre del contenedor, el volumen, el proyecto Compose, la base de datos y las
opciones de Mongo Express. Las contraseñas se solicitan sin mostrarlas en la consola.
Al final permite validar la configuración y arrancar ambos contenedores.

También puedes consultar la ayuda:

```bash
bash setup.sh --help
```

## Arranque

```powershell
Copy-Item .env.example .env
docker compose --env-file .env up -d
```

Los secretos locales se encuentran en `secrets/` y no se versionan. Como este compose
usa archivos locales para implementar Docker secrets, el script aplica permisos `644`
a los archivos para que el proceso dentro del contenedor pueda leerlos. El script crea
`mongo_express_mongodb_url` automáticamente a partir de las credenciales root para
que Mongo Express pueda conectarse sin exponerlas en `.env`. Para otro entorno,
copia las plantillas `.example` y sustituye sus valores antes de arrancar el contenedor.

Si ya ejecutaste una versión anterior del script y aparece `Permission denied`, corrige
los permisos y recrea los contenedores:

```bash
chmod 755 secrets
chmod 644 secrets/mongo_root_username secrets/mongo_root_password secrets/mongo_express_mongodb_url
docker compose --env-file .env down
docker compose --env-file .env up -d --force-recreate
```

## Opciones configurables

Edita `.env` para cambiar:

- `COMPOSE_PROJECT_NAME`: nombre del proyecto Compose y de su red.
- `MONGO_CONTAINER_NAME`: nombre del contenedor.
- `MONGO_IMAGE_TAG`: versión de la imagen de MongoDB.
- `MONGO_HOST_PORT`: puerto publicado en el host.
- `MONGO_VOLUME_NAME`: volumen donde se conserva `/data/db`.
- `MONGO_DATABASE`: base inicial usada por la imagen al inicializar el volumen.
- `MONGO_EXPRESS_IMAGE_TAG`: versión de Mongo Express.
- `MONGO_EXPRESS_CONTAINER_NAME`: nombre del contenedor de la interfaz web.
- `MONGO_EXPRESS_HOST_PORT`: puerto web publicado, por defecto `8081`.
- `MONGO_EXPRESS_COOKIE_SECRET`: secret para cookies de sesión.
- `MONGO_EXPRESS_SESSION_SECRET`: secret para sesiones web.

Mongo Express queda disponible en `http://localhost:8081` y utiliza el usuario y la
contraseña root de MongoDB para el acceso web. Sus credenciales y la URL interna de
MongoDB se montan desde `secrets/`; no se guardan en `.env`. El compose define tanto
el servicio `mongodb` como el alias de red `mongo`, porque algunas versiones de la
imagen `mongo-express` usan `mongo:27017` durante su arranque.

Para crear otra instancia en paralelo, usa otro `.env`, cambia al menos
`COMPOSE_PROJECT_NAME`, `MONGO_CONTAINER_NAME`, `MONGO_HOST_PORT` y
`MONGO_VOLUME_NAME`, y arranca el mismo compose con ese archivo.