# ¿Cómo crear nuestra propia imagen?

Un **Dockerfile** es un archivo de texto escrito en un formato específico que Docker puede entender. Consiste en instrucciones (`FROM`, `RUN`, `COPY`...) y argumentos.

Supongamos que desarrollamos una aplicación sencilla con Flask. Los pasos necesarios para ejecutarla correctamente en Ubuntu serían:

1. Sistema operativo Ubuntu  
2. Actualizar el repositorio de `apt`  
3. Instalar dependencias usando `apt`  
4. Instalar dependencias de Python usando `pip`  
5. Copiar el código fuente a la carpeta `/opt`  
6. Ejecutar el servidor web usando el comando `flask`

Nuestro Dockerfile podría verse así:

```dockerfile
FROM Ubuntu

RUN apt-get update
RUN apt-get install python 

RUN pip install flask
RUN pip install flask-mysql

COPY . /opt/source-code

ENTRYPOINT FLASK_APP=/opt/source-code/app.py flask run
```

- La primera línea debe ser una imagen de un sistema operativo o de otra imagen Docker. Todos los Dockerfile deben empezar con la instrucción `FROM`.  
- El comando `RUN` le indica a Docker que ejecute un comando en esa imagen base.  
- El comando `COPY` simplemente copia archivos desde el sistema local a la imagen de Docker.  
- Finalmente, `ENTRYPOINT` nos permite especificar un comando que se ejecutará cuando la imagen se ejecute como contenedor.

---

## Arquitectura por capas

Cuando Docker construye imágenes, lo hace siguiendo una arquitectura por capas. Cada línea de instrucción crea una nueva capa en la imagen Docker con los cambios respecto a la capa anterior.

En el ejemplo anterior, tenemos 5 capas:
1. Capa base de Ubuntu  
2. Cambios en los paquetes `apt`  
3. Cambios en los paquetes `pip`  
4. Código fuente  
5. Actualización del *entrypoint* con el comando `flask`

Si un paso falla, este tipo de arquitectura permite reconstruir la imagen reutilizando las capas anteriores desde caché y continuar construyendo las capas restantes. Lo mismo ocurre si añadimos pasos adicionales en el Dockerfile.

---

## CMD vs ENTRYPOINT

En un Dockerfile, tanto **CMD** como **ENTRYPOINT** definen qué se ejecuta cuando arranca un contenedor, pero se comportan de forma diferente:

- **CMD**: comando por defecto — se puede sobrescribir fácilmente al ejecutar el contenedor.  
- **ENTRYPOINT**: comando principal — siempre se ejecuta, pero se le pueden pasar argumentos adicionales.

### Usando `CMD`

```dockerfile
FROM alpine
CMD ["echo", "Hello from CMD"]
```
- `FROM alpine` → Usa la imagen ligera Alpine Linux como base.  
- `CMD ["echo", "Hello from CMD"]` → Establece el **comando por defecto** que se ejecutará al iniciar el contenedor.  
  Utiliza la **forma exec** (array JSON), que evita pasar por un shell y permite que Docker envíe señales directamente al proceso.

```bash
docker build -t cmd-example .
```
- `docker build` → Crea una imagen a partir del Dockerfile en el directorio actual.  
- `-t cmd-example` → Etiqueta la imagen como `cmd-example`.  
- `.` → Usa la carpeta actual como contexto de construcción.

```bash
docker run cmd-example
# Output: Hello from CMD
```
- Inicia un contenedor desde `cmd-example`.  
  Como no se pasa ningún comando, se ejecuta el `CMD` definido en la imagen.

```bash
docker run cmd-example echo "Overridden!"
# Output: Overridden!
```
- **Sobrescribe** el `CMD` de la imagen en tiempo de ejecución con el comando `echo "Overridden!"`.

### Usando `ENTRYPOINT`

```dockerfile
FROM alpine
ENTRYPOINT ["echo", "Hello from ENTRYPOINT"]
```
- `ENTRYPOINT ["echo", "Hello from ENTRYPOINT"]` → Define el **comando principal** que siempre se ejecuta al iniciar el contenedor.  
  Cualquier argumento pasado en `docker run` se **añade** al final de este comando.

```bash
docker build -t entrypoint-example .
```
- Construye la imagen y la etiqueta como `entrypoint-example`.

```bash
docker run entrypoint-example
# Output: Hello from ENTRYPOINT
```
- Ejecuta el contenedor; sin argumentos adicionales, imprime el mensaje fijo.

```bash
docker run entrypoint-example "and more text"
# Output: Hello from ENTRYPOINT and more text
```
- Pasa `"and more text"` como argumento adicional al comando de `ENTRYPOINT`, dando como resultado:
  ```
  echo Hello from ENTRYPOINT and more text
  ```

---

## Tabla resumen

| Característica             | CMD                                                     | ENTRYPOINT                                   |
|----------------------------|---------------------------------------------------------|----------------------------------------------|
| Rol                        | Comando por defecto                                     | Comando principal                            |
| Sobrescribir al ejecutar   | Sí (simplemente pasar otro comando)                     | No directamente; usar `--entrypoint`         |
| Acepta argumentos extra    | Solo si el comando sobrescrito los acepta               | Los argumentos se añaden al entrypoint       |
| Uso típico                 | Proporcionar comandos por defecto que se puedan cambiar | Comando fijo con argumentos flexibles        |
