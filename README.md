# Docker containers for development for dispossable databases

## Content

1. MySQL
2. MongoDB
3. NEO4J

# Development - MySQL

Container includes adminer as web UI

Run docker compose for mysql project:

```bash
docker compose -f mysql/docker-compose.yml up -d --build
```

Copy the directory with the secrets
```powershell
scp -r "C:\Users\digal\Documents\Python\docker_dbdev\mysql\secrets" luismdz@192.168.10.115:/home/luismdz/devdatabases/mysql/
```

# Development - MongoDB

MongoDB se ejecuta desde `mongodb/docker-compose.yml`. El usuario y la contraseña
se proporcionan mediante Docker secrets y las opciones no sensibles se configuran
en un archivo `.env`.

Para crear todos los archivos mediante una interfaz interactiva, ejecuta desde la
carpeta `mongodb`:

```bash
bash setup.sh
```

El script pregunta los valores de los secrets y de `.env`, crea los directorios,
establece los permisos necesarios y ofrece validar la configuración y arrancar MongoDB.
También configura Mongo Express, disponible en `http://localhost:8081`, protegido
con las mismas credenciales root almacenadas como secrets.

En Docker Compose local los secrets se montan desde archivos del host. Por eso el
script deja los archivos de `secrets/` con permisos `644`, necesarios para que los
procesos de los contenedores puedan leerlos. En Docker Swarm se pueden usar secrets
nativos con permisos más restrictivos.

## Configurar los secrets

Los archivos requeridos son:

- `mongodb/secrets/mongo_root_username`
- `mongodb/secrets/mongo_root_password`

Las plantillas versionables se encuentran en:

- `mongodb/secrets/mongo_root_username.example`
- `mongodb/secrets/mongo_root_password.example`

Para crear los archivos locales a partir de las plantillas:

```powershell
Copy-Item mongodb/secrets/mongo_root_username.example mongodb/secrets/mongo_root_username
Copy-Item mongodb/secrets/mongo_root_password.example mongodb/secrets/mongo_root_password
```

Sustituye los valores de los archivos sin incluir comillas ni espacios adicionales.
Los archivos reales están excluidos de Git mediante `.gitignore`.

## Configurar `.env`

Copia el archivo de ejemplo:

```powershell
Copy-Item mongodb/.env.example mongodb/.env
```

Las variables disponibles son:

- `COMPOSE_PROJECT_NAME`: nombre del proyecto Compose y de la red.
- `MONGO_CONTAINER_NAME`: nombre del contenedor.
- `MONGO_IMAGE_TAG`: versión de la imagen de MongoDB.
- `MONGO_HOST_PORT`: puerto publicado en el host.
- `MONGO_VOLUME_NAME`: volumen persistente para los datos.
- `MONGO_DATABASE`: nombre de la base inicial.

Para ejecutar varias instancias en paralelo, usa archivos `.env` distintos y cambia
como mínimo `COMPOSE_PROJECT_NAME`, `MONGO_CONTAINER_NAME`, `MONGO_HOST_PORT` y
`MONGO_VOLUME_NAME`.

## Validar la configuración

Desde la raíz del repositorio, comprueba primero la configuración interpolada:

```powershell
docker compose --env-file mongodb/.env -f mongodb/docker-compose.yml config --quiet
```

Si el comando termina sin salida y con código `0`, la sintaxis, las variables y las
referencias a los secrets son válidas. Para arrancar y revisar el estado:

```powershell
docker compose --env-file mongodb/.env -f mongodb/docker-compose.yml up -d
docker compose --env-file mongodb/.env -f mongodb/docker-compose.yml ps
```

El servicio debe aparecer como `healthy`. Si no llega a ese estado, revisa los
mensajes de inicialización:

```powershell
docker compose --env-file mongodb/.env -f mongodb/docker-compose.yml logs mongodb
```

El healthcheck ejecuta `db.adminCommand('ping')` usando las credenciales montadas
en `/run/secrets`. También puedes comprobar el acceso directamente:

```powershell
docker compose --env-file mongodb/.env -f mongodb/docker-compose.yml exec mongodb mongosh --quiet --username admin --password --authenticationDatabase admin --eval "db.adminCommand('ping')"
```

## Arrancar el contenedor

Con Docker Desktop o el engine de Docker iniciado, ejecuta:

```powershell
docker compose --env-file mongodb/.env -f mongodb/docker-compose.yml up -d
```

La primera inicialización crea el usuario administrador y configura `MONGO_DATABASE`.
El volumen conserva los datos entre reinicios; si cambias el nombre de la base o las
credenciales después de la primera ejecución, debes usar un volumen nuevo o migrar
los datos explícitamente.