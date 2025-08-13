# ¿Cómo crear un `docker-compose.yml`?

## 1. ¿Qué es Docker Compose?
Docker Compose es una herramienta para **definir y gestionar múltiples contenedores** con un solo archivo (`docker-compose.yml`).  
En vez de ejecutar múltiples `docker run ...`, defines los servicios y su configuración en un archivo YAML y los levantas con un solo comando.

## 2. Estructura básica
Un archivo `docker-compose.yml` está escrito en formato **YAML** y suele seguir esta estructura:

```yaml
version: "3.9"   # Versión del formato de Compose
services:        # Lista de servicios (cada uno será un contenedor)

  nombre_servicio:
    image: nombre_imagen:tag  # Imagen a usar (o usar "build" para construir)
    build: ./ruta_dockerfile  # Carpeta con el Dockerfile (opcional si usas image)
    container_name: nombre_personalizado
    ports:
      - "puerto_host:puerto_contenedor"
    environment:              # Variables de entorno
      VAR1: valor1
      VAR2: valor2
    volumes:                  # Volúmenes o carpetas compartidas
      - ./carpeta_local:/carpeta_en_contenedor
    depends_on:                # Dependencias de inicio
      - otro_servicio
```

---

## 3. Ejemplo sencillo: aplicación + base de datos

```yaml
version: "3.9"
services:
  app:
    build: ./app
    ports:
      - "3000:3000"
    environment:
      DATABASE_URL: postgres://user:pass@db:5432/mydb
    depends_on:
      - db

  db:
    image: postgres:15
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: pass
      POSTGRES_DB: mydb
    volumes:
      - db_data:/var/lib/postgresql/data

volumes:
  db_data: {}
```

---

## 4. Ejemplo con redes personalizadas

```yaml
version: "3.9"
services:
  frontend:
    build: ./frontend
    ports:
      - "8080:80"
    networks:
      - frontnet

  backend:
    build: ./backend
    ports:
      - "5000:5000"
    networks:
      - frontnet
      - backnet

  db:
    image: mysql:8
    environment:
      MYSQL_ROOT_PASSWORD: rootpass
    networks:
      - backnet

networks:
  frontnet:
  backnet:
```

> Ejemplo de aplicación real: [Docker Compose Examples](./Docker%20Compose%20Examples/docker-compose.yml)
---

## 5. Buenas prácticas al construir `docker-compose.yml`
- **Indentación**: usa siempre **2 espacios**, nunca tabs.
- **Variables de entorno**: puedes usar un archivo `.env` y referenciarlas así:
  ```yaml
  environment:
    DB_USER: ${DB_USER}
  ```
- **Volúmenes nombrados**: úsalos para persistir datos entre reinicios (`volumes:` al final del archivo).
- **Nombres de servicio**: dentro de la red de Compose, el nombre del servicio sirve como *hostname*.
- **Agrupa por contexto**: pon juntos los servicios que estén relacionados y define redes si necesitas aislar tráfico.
