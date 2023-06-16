PostgreSQL SSL certificate
============================

Description
-----------------------------
This repository contains the PostgreSQL database creation with Docker and check authentication through SSL certificate.

Procedure
-----------------------------
Below are described the main steps that are required in order to manage access to the PostgreSQL database with SSL certificate.

Stages
-----------------------------

### 1. Verify if the SSL certificate exists

#### Verify existing containers
`docker ps`

#### Enter in container
`docker exec -it <docker_id> sh`

#### Enter in Postgres container
`psql -h localhost -p 5433 -U postgres`

#### Update container
`apk update`

#### Install Postgres in container
`apk add postgresql`

#### Install code editor
`apt-get update && apt-get install -y vim`

#### Verify if the SSL certificate exists
`openssl s_client -connect localhost:5432 -showcerts`

### 2. Create the SSL certificate

#### Create the SSL certificate
`openssl req -new -x509 -days 365 -nodes -out server.crt -keyout server.key`

#### Move the SSL certificate
`mv server.crt server.key /var/lib/postgresql/data/`

#### Configure the server to use SSL certificate
`echo "ssl=on" >> /var/lib/postgresql/data/postgresql.conf`

#### Edit the code
`vim /var/lib/postgresql/data/postgresql.conf`

#### Restart the PostgreSQL server
`pg_ctlcluster 14 main restart`

#### Verify that SSL is enabled by connecting to the PostgreSQL server
`psql "sslmode=require host=localhost port=5432 dbname=mydatabase user=postgres password=password"`

### 3. Change the SSL certificate

#### Generate SSL certificate
`openssl req -new -text -out new-server.req -keyout new-server.key -subj "/CN=localhost"`

`openssl x509 -req -in new-server.req -text -days 365 -out new-server.crt -signkey new-server.key`

#### Move the SSL certificate
`docker cp new-server.key my-container:/ssl/new-server.key`

`docker cp new-server.crt my-container:/ssl/new-server.crt`

#### Modify postgres.conf file
`docker exec -it my-container vim /etc/postgresql/13/main/postgresql.conf`

#### Add to the postgres.conf file
`ssl_cert_file = '/ssl/new-server.crt'`

`ssl_key_file = '/ssl/new-server.key'`

#### Restart the Postgres server
`docker exec -it my-container pg_ctlcluster 13 main restart`
