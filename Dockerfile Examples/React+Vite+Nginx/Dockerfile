# Etapa 1: Build
# Usa una imagen oficial de Node.js basada en Alpine Linux (ligera).
# Le damos el alias builder para poder usar sus archivos más adelante en la etapa de producción.
FROM node:lts-alpine AS builder 

# Crea y define /app como el directorio de trabajo dentro del contenedor.
# Todo lo que se copie o ejecute a partir de aquí será relativo a esa carpeta.
WORKDIR /app

# Copia los archivos package.json y package-lock.json desde tu máquina al contenedor.
# Esto permite instalar las dependencias antes de copiar el resto del código (optimiza el uso de caché).
COPY package*.json ./

# Instala todas las dependencias del proyecto usando npm.
# Esto incluye tanto las de desarrollo como las necesarias para compilar (como Vite).
RUN npm install

# Copia todo el proyecto al contenedor (desde el host al contenedor en /app).
# Esto incluye src, index.html, vite.config.js, etc.
COPY . .

# Ejecuta vite build, que genera los archivos estáticos optimizados en la carpeta /app/dist.
RUN npm run build

# Etapa 2: Servidor Nginx
# Usa una imagen oficial de Nginx basada en Alpine (muy liviana).
# Aquí ya no se necesita Node.js, solo un servidor para servir los archivos estáticos.
FROM nginx:alpine

#  Borra los archivos por defecto que vienen con Nginx (como la página de bienvenida).
# Así dejas el directorio limpio para nuestra app.
RUN rm -rf /usr/share/nginx/html/*

#  Copia los archivos construidos en la etapa 1 (/app/dist) al directorio de Nginx que sirve contenido web: /usr/share/nginx/html.
# Esto es lo que el navegador verá cuando acceda a la app.
COPY --from=builder /app/dist /usr/share/nginx/html

# Configuración personalizada de Nginx:
# Sobrescribe la configuración por defecto de Nginx con una personalizada.
# Esto es fundamental para que tu SPA funcione correctamente con rutas internas.
COPY nginx.conf /etc/nginx/conf.d/default.conf

# Indica que el contenedor usará el puerto 80 (el que Nginx escucha).
# Esto no abre el puerto, pero sirve como documentación para herramientas como Docker Compose o Portainer.
EXPOSE 80

# Este es el comando que se ejecutará cuando el contenedor se inicie.
# daemon off indica que Nginx se ejecute en primer plano (requisito de Docker: el proceso principal debe mantenerse activo).
CMD ["nginx", "-g", "daemon off;"]
