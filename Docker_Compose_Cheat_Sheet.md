
## Docker Compose Cheat Sheet

Build images:

```bash
docker compose build
```

Start containers:

```bash
docker compose start
```

Stop containers:

```bash
docker compose stop
```

Build and start:

```bash
docker compose up -d
```

List what's running:

```bash
docker compose ps
```

Remove from memory:

```bash
docker compose rm
```

Stop and remove:

```bash
docker compose down
```

Get the logs:

```bash
docker compose logs
```

Run a command in a container:

```bash
docker compose exec [container] bash
```

---

## Docker Compose V2 - New Commands

Run an instance as a project:

```bash
docker compose --project-name test1 up -d
```

Shortcut:

```bash
docker compose -p test2 up -d
```

List running projects:

```bash
docker compose ls
```

Copy files from the container:

```bash
docker compose cp [containerID]:[SRC_PATH] [DEST_PATH]
```

Copy file to the container:

```bash
docker compose cp [SRC_PATH] [containerID]:[DEST_PATH]
```

---

## Docker Compose examples

```bash
# build the service
docker compose build

# builds, (re)creates, starts, attaches to containers for a service
docker compose up

#list the services
docker compose ps

#bring down what was created by UP
docker compose down
```

---

## Useful commands for development and debugging

### View real-time logs of a specific service

```bash
docker compose logs -f backend
```

```bash
docker compose logs -f frontend
```

### View all logs in real time

```bash
docker compose logs -f
```

### Access a container (interactive mode)

```bash
docker compose exec backend /bin/bash
```

(If bash is not available, use /bin/sh)

### View environment variables inside the container

```bash
env
```

(Once inside with exec)

### Restart a service

```bash
docker compose restart backend
```

### Rebuild an image and start the service

```bash
docker compose up --build backend
```

### Remove containers, volumes, and orphan networks
```bash
docker compose down --volumes --remove-orphans
```
Stops and removes containers, volumes, and unused orphan networks.
Useful to fix errors like network not found or to fully reset the project.
This deletes persistent data. Use with caution!

### Passing specific environment variables

```bash
ENV_VAR=value docker compose up --build
```

### Start a specific .yml file
```bash
docker compose -f docker-compose.dev.yml up --build
```
Starts the services defined in a specific Docker Compose YAML file (in this case, docker-compose.dev.yml) and rebuilds the images before starting.

