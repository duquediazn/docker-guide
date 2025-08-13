# Usar la imagen oficial de Python en su versión ligera como base
FROM python:3-slim

# Exponer el puerto 8000 al host (la app escuchará en este puerto)
EXPOSE 8000

# Evitar que Python genere archivos .pyc en disco
ENV PYTHONDONTWRITEBYTECODE=1

# Asegurar que los logs se muestren en tiempo real (sin buffer de salida)
ENV PYTHONUNBUFFERED=1

# Copiar el archivo de requisitos al contenedor
COPY requirements.txt .

# Instalar las dependencias de Python sin guardar en caché los paquetes descargados
RUN python -m pip install --no-cache-dir -r requirements.txt

# Establecer el directorio de trabajo en /app dentro del contenedor
WORKDIR /app

# Copiar todo el proyecto al contenedor (todo será relativo a /app)
COPY . /app

# Crear un usuario sin privilegios de root con un UID específico y darle propiedad del directorio /app
# Esto mejora la seguridad al no ejecutar la aplicación como root
RUN adduser -u 5678 --disabled-password --gecos "" appuser && chown -R appuser /app

# Cambiar al usuario sin privilegios
USER appuser

# Definir el comando por defecto para ejecutar la aplicación usando Gunicorn
# Inicia 4 procesos de trabajo, enlaza al puerto 8000 y usa la clase de trabajador Uvicorn (para apps ASGI como FastAPI)
CMD ["gunicorn", "--workers", "4", "--bind", "0.0.0.0:8000", "-k", "uvicorn.workers.UvicornWorker", "app.main:app"]
