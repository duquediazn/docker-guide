## Running and Stopping

Pull an image from a registry:

```bash
docker pull [imageName]
```

Run image as a container:

```bash
docker run [imageName]
```

Detached mode. Run the cointainer in the background, giving us the terminal back.

```bash
docker run -d [imageName]
```

Set an environment variable within the container

```bash
docker run -e VAR_NAME=value [imageName]
```

Start stopped containers:

```bash
docker start [containerName]
```

List running containers:

```bash
docker ps
```

List running and stopped containers:

```bash
docker ps -a
```

Stop containers (the container will still be on memory)

```bash
docker stop [containerName]
```

Kill container:

```bash
docker kill [containerName]
```

Get image info:

```bash
docker image inspect [imageName]
```

---

## Limits

Max memory:

```bash
docker run --memory="256m" nginx
```

Max CPU:

```bash
docker run --cpus=".5" nginx
```

### Example of pulling and running a container

```bash
docker run --publish 80:80 --name webserver nginx
```

- _publish 80:80_: maps the host port to the container listening port (-p also valid)
- _--name webserver_: container custom local name
- _nginx_: container image in the Docker registry

---

## Attach Shell

Attach shell (to be able to execute commands in the running container):

```bash
docker run -it nginx -- /bin/bash
```

Attach powershell:

```bash
docker run -it -- microsoft/powershell:nanoserver pwsh.exe
```

Attach to a running container:

```bash
docker container exec -it [containerName] -- bash
```

---

## Cleaning up

Removes stopped containers:

```bash
docker rm [containerName]
```

Removes all stopped containers:

```bash
docker rm $(docker ps -a -q)
```

List images:

```bash
docker images
```

Deletes the image:

```bash
docker rmi [imageName]
```

Removes all images not in use by any containers:

```bash
docker system prune -a
```

---

## Building

Builds an image using a Dockerfile located in the same folder:

```bash
docker build -t [name:tag] .
```

Builds an image using a Dockerfile located in a different folder:

```bash
docker build -t [name:tag] -f [fileName]
```

Tag an existing image:

```bash
docker tag [imageName] [image:tag]
```

### Example of building an image from a dockerfile

```bash
#build
docker build -t webserver-image:v1 .

#run
docker run -d -p 8080:80 webserver-image:v1

#display
curl localhost:8080
```

---

## Networks

Run with an specific network attached to the container

```bash
docker run [imageName] --network=network-name
```

Example: 
```bash
docker run Ubuntu --network=host
```

User-defined networks
```bash
docker network create \
  --driver bridge \
  --subnet 182.18.0.0/16
  custom-isolated-network
```

To list all the networks
```bash
docker network ls
```

---

## Volumes

Create a new volume:
```bash
docker volume create [volumeName]
```

List the volumes:
```bash
docker volume ls
```

Display volume info:
```bash
docker volume inspect [volumeName]
```

Delete a volume:
```bash
docker volume rm [volumeName]
```

Delete all unused volumes:
```bash
docker volume prune
```

### Mapping a volume

**Using `-v` (`--volume`)**:
```bash
# create a volume
docker volume create myvol

# inspect the volume
docker volume inspect myvol

# list the volumes
docker volume ls

# run a container with a volume
docker run -d --name devtest -v myvol:/app nginx:latest
```
- `-v myvol:/app` → mounts the Docker-managed volume `myvol` into `/app` inside the container.

**Using `--mount`**:
```bash
docker run -d --name devtest --mount type=volume,src=myvol,dst=/app,ro nginx:latest
```
- `ro` → read-only mode.


### Bind mounts (map a local folder)

Instead of a Docker-managed volume, you can mount a local host folder:

**Using `-v`**:
```bash
docker run -d --name devtest -v /path/on/host:/app nginx:latest
```
*(On Windows: `docker run -d --name devtest -v //d/test:/app nginx:latest`)*

**Using `--mount`**:
```bash
docker run -d --name devtest --mount type=bind,source=/path/on/host,target=/app nginx:latest
```

**Note:** Bind mounts are not recommended for production in many cases, as changes on the host affect the container immediately and can cause permission issues.

---

## Docker Registry (Docker Hub)

Publish to Docker:

```bash
#login to Docker Hub
docker login -u <username> -p <password>

#tag an image previously built
docker tag my_image <yourRegistryName>/my_image:<tag>

#push the image
docker push <yourRegistryName>/my_image:<tag>

#pull the image
docker pull <yourRegistryName>/my_image:<tag>
```

---

## Other interesting commands
### View an image's history
```bash
docker history <image_name_or_id>
```
Shows the history of an image, including the commands used to create each layer, their sizes, and timestamps.
Useful for understanding how an image was built or for debugging large image sizes.

Example:
```bash
docker history node:20-alpine
```

### Backup y restauración de la base de datos PostgreSQL (volumen)

Backup manual usando pg_dump (requiere que el contenedor esté corriendo):

```bash
docker exec -t tabulae-db pg_dump -U tabulae_user tabulae_data > backup.sql

```

Restaurar desde backup:
```bash
docker exec -i tabulae-db psql -U tabulae_user -d tabulae_data < backup.sql
```