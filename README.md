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