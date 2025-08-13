# Resumen de los conceptos principales de Docker

## ¿Qué es una imagen en Docker?

Fuente oficial: [What is an image? - Docker docs](https://docs.docker.com/get-started/docker-concepts/the-basics/what-is-an-image/)

Una imagen de Docker es una plantilla de solo lectura con instrucciones para crear un contenedor. Incluye:

- Un sistema de archivos (como una distro de Linux mínima)
- Dependencias necesarias (librerías, herramientas, etc.)
- El código de tu aplicación
- Instrucciones específicas de configuración (como el comando que se ejecuta al iniciar el contenedor)

Piensa en una imagen como un "snapshot" congelado de una app lista para ejecutarse. Las imágenes se construyen generalmente usando un archivo Dockerfile. Las imágenes están formadas por capas, y Docker las reutiliza para ser más eficiente. Por ejemplo, si 10 imágenes usan ubuntu:20.04 como base, esa capa solo se descarga una vez.

--- 

## ¿Qué es un contenedor en Docker?

Fuente oficial: [What is a container? - Docker docs](https://docs.docker.com/get-started/docker-concepts/the-basics/what-is-a-container/)

Un contenedor es una instancia en ejecución de una imagen. Cuando lanzas una imagen, Docker crea un contenedor a partir de ella. El contenedor:

- Tiene su propio sistema de archivos (basado en la imagen)
- Está aislado del host (mediante namespaces y cgroups del kernel de Linux)
- Puede tener sus propias variables, puertos expuestos, volúmenes, etc.

Es como lanzar una app dentro de una caja cerrada, con todo lo necesario para funcionar.

Diferencia clave:

- Imagen = receta (estática)
- Contenedor = instancia en ejecución (dinámica y viva)

### Docker Vs VM

- Artículo: [Docker vs Virtual Machine (VM) – Key Differences You Should Know - freeCodeCamp](https://www.freecodecamp.org/news/docker-vs-vm-key-differences-you-should-know/)
- Artículo: [Understanding Virtual Machines vs Docker Containers: A Technical Comparison - Medium](https://medium.com/@ravipatel.it/understanding-virtual-machines-vs-docker-containers-a-technical-comparison-241f370b2076)
- Artículo: [Docker Namespace and Cgroups - Medium](https://medium.com/@kasunmaduraeng/docker-namespace-and-cgroups-dece27c209c7)

| Concepto           | Contenedor Docker             | Máquina Virtual                               |
| ------------------ | ----------------------------- | --------------------------------------------- |
| Kernel             | Usa el del host               | Tiene su propio kernel                        |
| SO completo        | ❌ No                         | ✅ Sí                                         |
| Ligero             | ✅ Muy                        | ❌ No (ocupa más recursos)                    |
| Aislamiento        | ✅ namespaces, cgroups        | ✅ Completo, más pesado                       |
| Tiempo de arranque | Casi instantáneo              | Lento (arranca SO)                            |
| Uso común          | Apps aisladas, microservicios | Entornos de desarrollo, infra más tradicional |

> Aquí puedes ver la lista de los comandos más importantes de Docker: [Docker_CLI_Cheat_Sheet.md](./Docker_CLI_Cheat_Sheet.md)

---

## ¿Qué es un Dockerfile?

- Fuente oficial:[ Dockerfile reference - Docker docs](https://docs.docker.com/reference/dockerfile/)
- Fuente oficial:[ Writing a Dockerfile - Docker docs](https://docs.docker.com/get-started/docker-concepts/building-images/writing-a-dockerfile/)

Un Dockerfile es un archivo de texto plano que contiene instrucciones ordenadas que Docker ejecuta para construir una imagen. Cada instrucción representa una capa de la imagen resultante. No es un lenguaje como Python, Bash o SQL, pero es una especificación propia de Docker.

Cada instrucción del Dockerfile genera una capa nueva en la imagen. Por eso es recomendable agrupar acciones relacionadas (por ejemplo, en un solo RUN) para minimizar el tamaño final.

### Estructura básica de un Dockerfile

```
# Imagen base
FROM python:3.11-slim

# Directorio de trabajo dentro del contenedor
WORKDIR /app

# Copiar archivos
COPY requirements.txt .

# Instalar dependencias
RUN pip install -r requirements.txt

# Copiar el resto del código
COPY . .

# Comando por defecto
CMD ["python", "main.py"]
```

### Instrucciones principales

| Instrucción  | Qué hace                                                                               |
| ------------ | -------------------------------------------------------------------------------------- |
| `FROM`       | Define la imagen base (obligatoria y debe ser la primera)                              |
| `WORKDIR`    | Define el directorio de trabajo en la imagen                                           |
| `COPY`       | Copia archivos desde tu máquina al contenedor                                          |
| `RUN`        | Ejecuta comandos durante la construcción (como `apt-get`, `pip install`, etc.)         |
| `CMD`        | Define el comando que se ejecutará por defecto cuando el contenedor arranque           |
| `EXPOSE`     | Documenta el puerto que expone la app                                                  |
| `ENV`        | Define variables de entorno                                                            |
| `ARG`        | Define argumentos para la construcción de la imagen                                    |
| `ENTRYPOINT` | Similar a `CMD`, pero más rígido; útil para imágenes que se comportan como ejecutables |

> Para saber más sobre cómo crear un Dockerfile y cómo funciona la arquitectura por capas de Docker: [Dockerfile_explained.md](./Dockerfile_explained.md)

---

## Docker networking
- Fuente oficial: [Networking overview - Docker docs](https://docs.docker.com/engine/network/)

Cuando instalamos Docker, se crean tres redes por defecto: bridge, none y host.
Bridge es la red predeterminada a la que se conecta un contenedor si no se especifica otra. Si queremos asociar el contenedor a otra red, debemos indicarlo usando el parámetro de línea de comandos --network.

Example: 
```
docker run Ubuntu --network=host
```

### Bridge
Es la red interna privada creada por Docker en el host. Todos los contenedores se conectan a esta red por defecto y reciben una dirección IP interna (normalmente en el rango 172.17.x.x).
Los contenedores pueden comunicarse entre sí usando esta IP interna. Para acceder a cualquiera de estos contenedores desde fuera, es necesario mapear puertos del contenedor a puertos del host de Docker.

### Host
Otra forma de acceder a los contenedores desde fuera es asociarlos directamente a la red host. Esto elimina el aislamiento de red entre el host y el contenedor, por lo que no es necesario hacer mapeo de puertos.
Sin embargo, a diferencia de bridge, no podremos ejecutar varios contenedores que usen el mismo puerto en el mismo host, ya que ahora los puertos son comunes para todos.

### None
En este modo, el contenedor no se conecta a ninguna red y no tiene acceso ni a la red externa ni a otros contenedores.

> Para ver más comandos asociados a networking y cómo definir tus propias redes: [Docker_CLI_Cheat_Sheet.md](./Docker_CLI_Cheat_Sheet.md#networks)

Docker usa network namespaces de Linux para aislar las pilas de red de cada contenedor. Esto significa que cada contenedor puede tener sus propias interfaces, rutas y reglas de firewall, independientes del host y de otros contenedores. 

Para obtener información detallada de una red (contenedores conectados, rangos IP, drivers, etc.) se usa:

```
docker network inspect bridge
```

---

## Docker Storage
- Fuente oficial: [Storage - Docker docs](https://docs.docker.com/engine/storage/)

### Sistema de archivos básico

Cuando instalas Docker, crea una estructura de carpetas en el sistema (normalmente bajo `/var/lib/docker`) para guardar distintos tipos de datos:

- `overlay2` (o `aufs`, según el controlador de almacenamiento): aquí se almacenan las capas de las imágenes y los cambios de contenedores activos. Incluye estructuras como `lower`, `upper`, `merged` y `work`, que componen el sistema de archivos en capas unificado.
- `containers`: información interna de los contenedores en ejecución (logs, estado, etc.).
- `image`: metadatos de imágenes disponibles (como etiquetas, IDs, referencias).
- `volumes`: datos persistentes gestionados por Docker, que sobreviven al ciclo de vida de los contenedores.

#### Storage drivers en Docker

Docker usa **storage drivers** (*controladores de almacenamiento*) para gestionar el sistema de archivos en capas que usan imágenes y contenedores.

- Ejemplos: `overlay2` (recomendado en la mayoría de sistemas Linux), `aufs` (más antiguo), `btrfs`, `zfs`, `devicemapper`, `vfs`.
- El storage driver determina **cómo** se combinan las capas de las imágenes con la capa de escritura del contenedor usando la técnica *copy-on-write*.
- La carpeta que vemos en `/var/lib/docker/` (`overlay2`, `aufs`, etc.) corresponde al driver activo en nuestro sistema y contiene las capas y cambios de los contenedores.

**Cómo ver el driver que estamos usando:**
```bash
docker info | grep "Storage Driver"
```
Ejemplo de salida:
```
Storage Driver: overlay2
```

> **Nota:** `overlay2` es el driver por defecto en distribuciones modernas y ofrece mejor rendimiento y compatibilidad que `aufs`, que está en desuso.

#### ¿Cómo optimiza espacio el sistema de capas?
Docker usa un **sistema de archivos por capas** (copy-on-write). Como habíamos visto, cada instrucción del `Dockerfile` (por ejemplo, `RUN`, `ADD`, `COPY`) crea una **nueva capa** inmutable. Al construir una imagen:
- Si una capa **ya existe** en el host (porque otra imagen la usa), **no se descarga ni se crea de nuevo**: Docker **reutiliza** esa capa.
- Las capas se **comparten** entre imágenes que las necesiten, lo que reduce el uso de disco y acelera las descargas/builds.
- Cuando un contenedor modifica un archivo de una capa, no cambia la capa original: los cambios se guardan en la **capa superior de escritura** del contenedor (copy-on-write).

Consecuencias prácticas:
- Builds más rápidos gracias a la **caché de capas**.
- Menor consumo de almacenamiento cuando varias imágenes comparten base y dependencias.
- Cambios en pasos **anteriores** del `Dockerfile` invalidan la caché de las **capas posteriores**.

> Tip: Ordenar el `Dockerfile` para maximizar la caché (p. ej., instala dependencias de sistema antes de copiar tu código que cambia a menudo).

### Persistencia de datos - Volumes

- Fuente oficial: [Persisting data - Docker docs](https://docs.docker.com/get-started/workshop/05_persisting_data/)
- Fuente oficial: [Volumes - Docker docs](https://docs.docker.com/engine/storage/volumes/)

#### ¿Qué es un volumen en Docker?

Un volumen es una ubicación persistente en el sistema de archivos que Docker gestiona, y que se puede adjuntar a uno o varios contenedores.

#### ¿Para qué se usan?

1. **Persistir datos:** Los datos dentro de un contenedor se pierden al eliminarlo, pero un volumen puede conservarse incluso después de parar o borrar el contenedor.
2. **Compartir datos entre contenedores:** Dos contenedores pueden acceder al mismo volumen para intercambiar archivos (por ejemplo: logs, archivos temporales, etc.)
3. **Evitar que tus builds incluyan archivos pesados o sensibles:** Puedes mantener archivos como bases de datos, archivos de configuración, etc., fuera de la imagen.

#### Cómo montarlos en un contenedor**:  
Puedes usar `--volume` (o `-v`) o la forma más explícita `--mount`.  

Ejemplo con `-v`:
```bash
docker run -v myvol:/data:ro
```

Ejemplo con `--mount`:
```bash
docker run --mount type=volume,src=myvol,dst=/data,ro
```

> Para ver más comandos asociados a creación y gestión de volúmenes: [Docker_CLI_Cheat_Sheet.md](./Docker_CLI_Cheat_Sheet.md#volumes)

---

## ¿Qué es Docker Compose?

- Fuente oficial: [Overview of Docker Compose – Docker docs](https://docs.docker.com/compose/)
- Fuente oficial: [What is Docker Compose? - Docker docs](https://docs.docker.com/get-started/docker-concepts/the-basics/what-is-docker-compose/)

Docker Compose es una herramienta que permite definir y levantar múltiples contenedores de Docker a la vez, usando un archivo de configuración llamado docker-compose.yml. Se utiliza cuando una aplicación necesita varios servicios que deben funcionar en conjunto, como por ejemplo:

- un backend (como FastAPI),
- una base de datos (como PostgreSQL),
- u otros servicios complementarios (Redis, frontend, etc.).

Con Compose, podemos arrancarlos todos juntos con un solo comando:

```
docker compose up
```

### ¿Qué hace Compose exactamente?

- Lee el archivo docker-compose.yml
- Crea redes y volúmenes automáticamente
- Construye o descarga las imágenes necesarias
- Lanza cada contenedor con su configuración

> Para saber más sobre por qué Docker Compose es la repanocha: [Docker Tutorial for Beginners | Compose](https://youtu.be/fqMOX6JJhGo?si=5EMr3BbjQbw0LSvm&t=4590)
> Cheat Sheet de comandos docker compose: [Docker Compose Cheat Sheet](./Docker_Compose_Cheat_Sheet.md)

### ¿Qué es un archivo docker-compose.yml?

- Fuente oficial: [Compose file reference – Docker docs](https://docs.docker.com/reference/compose-file/)
- Fuente oficial: [Docker Compose specification - Compose spec](https://compose-spec.io/)

El archivo docker-compose.yml está escrito en formato YAML, que es un lenguaje de configuración muy legible (usa sangrías en lugar de llaves o corchetes).

Este archivo define:

- Los servicios (contenedores) a ejecutar
- Las variables de entorno
- Los puertos que se exponen
- Los volúmenes que se montan
- Las redes para conectar contenedores entre sí

Ejemplo básico:

```
version: "3.8"

services:
  backend:
    build: .
    ports:
      - "8000:8000"
    env_file:
      - .env
    depends_on:
      - db

  db:
    image: postgres:15
    environment:
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: admin123
      POSTGRES_DB: inventario
    volumes:
      - db_data:/var/lib/postgresql/data

volumes:
  db_data:
```

### ¿Qué es YAML?

YAML (YAML Ain’t Markup Language) es un lenguaje de configuración que se usa para archivos como docker-compose.yml.

- Usa indentación en lugar de llaves {} o corchetes []
- Es muy legible para humanos
- Es sensible a los espacios (no usar tabs)

### Ventajas de Docker Compose

| Característica              | ¿Por qué es útil?                                                    |
| --------------------------- | -------------------------------------------------------------------- |
| **Orquestación local**      | Puedes levantar toda la app con un solo comando                      |
| **Legibilidad**             | YAML es claro y fácil de entender                                    |
| **Reutilización**           | Puedes compartir y versionar tu `docker-compose.yml`                 |
| **Automatización**          | Define todo de forma declarativa (como Infraestructura como Código)  |
| **Red privada por defecto** | Los contenedores definidos pueden comunicarse por nombre de servicio |

> Para saber más sobre cómo crear un docker-compose.yml: [Docker_compose_explained.md](./Docker_compose_explained.md)

### Apps de prueba:

- [L09-04 Docker Compose](https://github.com/K8sAcademy/Fundamentals-HandsOn/tree/main/L09-04%20Docker%20Compose)
  Notas:
  - Para el segundo compose up, cambia el puerto a 5001.
  - Al usar el flag -f o --follow, los logs se actualizarán dinámicamente según el contenedor registre nuevas entradas.

* [L09-05 Docker Compose - Sample App](https://github.com/K8sAcademy/Fundamentals-HandsOn/tree/main/L09-05%20Docker%20Compose%20-%20Sample%20App)

---

## ¿Qué es Docker Registry?

Fuente oficial: [What is a registry? - Docker docs](https://docs.docker.com/get-started/docker-concepts/the-basics/what-is-a-registry/)

Un Docker Registry es un servicio para almacenar y distribuir imágenes de Docker. Puedes subir (push) y descargar (pull) imágenes desde allí.

¿Para qué se usan?

- Para compartir tus imágenes con otras personas o equipos
- Para automatizar despliegues (por ejemplo, desde GitHub Actions)
- Para mantener versiones de tus contenedores

### Docker Hub (el registry público por defecto)

Docker Hub es el registry oficial y más usado. Al hacer docker pull ubuntu o docker run python:3.11, estás descargando imágenes desde Docker Hub.

### Registries privados

Además de Docker Hub, existen registries privados donde puedes guardar tus imágenes:

| Registry                          | ¿Qué ofrece?                     |
| --------------------------------- | -------------------------------- |
| **GitHub Container Registry**     | Integración con GitHub           |
| **GitLab Container Registry**     | Integración con GitLab CI        |
| **Amazon ECR (AWS)**              | Registry privado en la nube      |
| **Google Artifact Registry**      | Registry de Google Cloud         |
| **Azure Container Registry**      | Registry de Azure                |
| **Harbor**                        | Registry privado self-hosted     |
| **Docker Registry (open source)** | Puedes montar tu propio registry |

---

## Docker Engine

- Fuente oficial: [Docker Engine - Docker docs](https://docs.docker.com/engine/)
- Fuente oficial: [Docker Overview - Docker docs](https://docs.docker.com/get-started/overview/)

Docker se compone principalmente de:

- **Docker CLI**: interfaz de línea de comandos que envía peticiones al daemon vía API REST.
- **Docker Daemon (`dockerd`)**: proceso principal que gestiona imágenes, contenedores, redes y volúmenes. Controla el ciclo de vida de los contenedores y se comunica con otros daemons si es necesario.
- **Containerd**: runtime de contenedores que el daemon utiliza para gestionar contenedores a bajo nivel.
- **Runc**: ejecuta contenedores siguiendo la especificación OCI (*Open Container Initiative*).
- **Storage Drivers**: gestionan el sistema de archivos en capas (OverlayFS, AUFS, etc.) y el mecanismo *copy-on-write*.
- **Network Drivers**: implementan la conectividad entre contenedores y hacia el exterior (bridge, host, overlay, none, etc.).

### Flujo básico
1. **Construcción**: el CLI envía al daemon la orden de crear una imagen a partir de un `Dockerfile`.  
   El daemon procesa cada instrucción como una capa inmutable, gestionada por el storage driver.
2. **Ejecución**: al ejecutar `docker run`, el daemon usa containerd/runc para crear el contenedor, aplicando:
   - **Namespaces**: para aislar procesos, red, usuarios, PIDs, etc.
   - **Cgroups**: para limitar recursos (CPU, memoria, I/O).
   - **Capas de sistema de archivos**: imagen base + capa de escritura temporal.
3. **Networking y almacenamiento**: el contenedor se conecta a la red definida (por defecto, `bridge`) y monta volúmenes si se han especificado.
4. **Finalización**: al parar/eliminar un contenedor, la capa de escritura se descarta salvo que se use un volumen.

> Aquí puedes ver una explicación un poco más detallada pero sencilla del funcionamiento interno de Docker: [Docker Tutorial for Beginners | Docker Engine](https://youtu.be/fqMOX6JJhGo?si=vgB8ZrLMP_pA3FGc&t=5978)
