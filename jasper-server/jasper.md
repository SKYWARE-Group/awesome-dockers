# Jasper Server CE


1. Create **docker network**

```bash
docker network create -d bridge jasper-dev-net
```

2. Create **PostgreSQL Server container**


```
docker run -e POSTGRES_PASSWORD=YOUR-POSTGRES-PASSWORD-HERE -e POSTGRES_HOST_AUTH_METHOD="password" -v /your-postgres-volume-here:/var/lib/postgresql/data --network=jasper-dev-net -p 127.0.0.1:1555:5432 --hostname=pg --name jasper-dev-pg -d --restart=unless-stopped postgres:latest
```

Notes
* `-e POSTGRES_HOST_AUTH_METHOD="password"` (config `hba.conf`)
* `-p 127.0.0.1:1555:5432` (set your own port to localhost on docker host)

3. After PostgreSQL container is created, **create databse and user for Jasper**
 - Enter interactively into the container 

```bash
docker exec -ti jasper-dev-pg /bin/bash
```

 - Enter into the postgres server

```bash
psql -U postgres
```

  - Execute following commands (create database and user)

```bash
CREATE DATABASE jasper_db;
CREATE USER jasper WITH PASSWORD 'YOUR-JASPER-USER-PASWWORD-HERE';
\c jasper_db
GRANT CREATE, USAGE ON SCHEMA public TO jasper;
```
  - Quit Psql and Container

```bash
\q
exit
```

4. Create **Jasper Server container**

```bash
docker run -d --name jasper-dev --hostname=jasper -p 127.0.0.1:8090:8080 --restart=unless-stopped -e JASPERREPORTS_PASSWORD="YOUR-JASPER-PASSWORD-HERE" --network=jasper-dev-net --volume /your-jasper-volume-here:/bitnami/jasperreports -e JASPERREPORTS_DATABASE_TYPE=postgresql -e JASPERREPORTS_DATABASE_HOST="jasper-dev-pg" -e JASPERREPORTS_DATABASE_PORT_NUMBER=5432 -e JASPERREPORTS_DATABASE_NAME=jasper_db -e JASPERREPORTS_DATABASE_USER=jasper -e JASPERREPORTS_DATABASE_PASSWORD="YOUR-JASPER-USER-PASWWORD-HERE" bitnami/jasperreports:latest
```

That's it!

Your server should be running at http://localhost:8090/jsaperserver/


