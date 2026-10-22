# Imagen con node + nginx para permitir ejecutar npm run build dentro del contenedor
FROM node:22-alpine AS build

# Instalar nginx
#RUN apk add --no-cache nginx

# Variables de entorno para el build
ENV VITE_API_URL=/api
ENV APP_DIR=/app
    #NGINX_HTML=/usr/share/nginx/html

WORKDIR ${APP_DIR}

# Copiar manifiestos e instalar dependencias (cacheable)
COPY package.json package-lock.json ./
RUN npm ci
# COPY package*.json ./
# RUN npm install

# Copiar el resto del código (también se montará por volumen en desarrollo)
# COPY . .
COPY index.html ./
COPY vite.config.js ./
COPY src ./src
COPY public ./public

# Compilar la aplicación
RUN npm run build

FROM nginx:alpine
ENV NGINX_HTML=/usr/share/nginx/html

# Configuración de nginx
#COPY nginx.conf /etc/nginx/http.d/default.conf
COPY nginx.conf /etc/nginx/conf.d/default.conf

# Copiar los archivos compilados
COPY --from=build /app/dist/ ${NGINX_HTML}/

# Entrypoint que permite: build on-start, modo dev, o comando arbitrario
#COPY entrypoint.sh /usr/local/bin/entrypoint.sh
#RUN chmod +x /usr/local/bin/entrypoint.sh

EXPOSE 80

#ENTRYPOINT ["/usr/local/bin/entrypoint.sh"]
#CMD ["serve"]