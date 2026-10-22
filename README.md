# Proyecto Final Docker: Laravel, React, MariaDB y Mailpit

## 1. Descripción
Este proyecto utiliza Docker Compose para Ejecutar 4 Servicios:
- **Backend:** Aplicación Laravel.
- **Frontend:** Aplicación React Compilada y servida mediante Nginx.
- **Base de datos:** MariaDB con Almacenamiento Persistente.
- **Correo:** Mailpit para Probar el Envío de Correos en Desarrollo.

## 2. Requisitos
- Git.
- Docker Engine o Docker Desktop con Integración para Docker Compose.
- Acceso a Docker Hub para Descargar las Imágenes necesarias.
- Puertos Disponibles: 8000, 8080, 5173, 3307, 1025 y 8025, o los Puertos Alternativos Configurados en `.env`.

## 3. Clonar el Repositorio
Clonar el Repositorio Publicado en GitHub:
```bash
git clone https://github.com/DiplomadoUNAM/entrega-final.git
cd entrega-final

## 4. Configurar las variables de entorno
Crear los Archivos de Configuración a partir de las Plantillas:
```bash
cp .env.example .env
cp Backend/.env.example Backend/.env
```

Editar ambos Archivos antes de continuar.
En el `.env` de la raíz:
- Configurar una Contraseña Segura para `DB_PASSWORD`.
- Mantener `DB_VOLUME_NAME=practica-final_sail-mariadb`, salvo que se decida utilizar otro Nombre.
- Ajustar los Puertos si alguno está Ocupado.

En `Backend/.env`:
- Configurar `DB_CONNECTION=mariadb`.
- Configurar `DB_HOST=mariadb`.
- Configurar `DB_PORT=3306`.
- Utilizar `DB_DATABASE=laravel` y `DB_USERNAME=sail`, o los valores que correspondan.
- Establecer en `DB_PASSWORD` la Misma Contraseña Configurada en el `.env` de la raíz.
- Mantener la Configuración de Correo `MAIL_MAILER=smtp`, `MAIL_HOST=mailpit` y `MAIL_PORT=1025`.

## 5. Construir las Imágenes
Desde la raíz del proyecto:
```bash
docker compose build
```

## 6. Instalar las Dependencias de Laravel
Ejecutar Composer Dentro del Contenedor:
```bash
docker compose run --rm --no-deps backend composer install
```

Generar la Clave de la Aplicación:
```bash
docker compose run --rm --no-deps backend php artisan key:generate
```
Este comando configura `APP_KEY` en `Backend/.env`.

## 7. Crear el Volumen Persistente
Crear el Volumen de MariaDB antes de Iniciar los Servicios:
```bash
docker volume create practica-final_sail-mariadb
```

El Nombre debe Coincidir con `DB_VOLUME_NAME` en el `.env` de la Raíz. Si se Cambia ese Valor, Crear el Volumen con el Nombre Correspondiente.

Si el Volumen ya Existe, Docker lo conservará; NO es necesario Borrarlo.

## 8. Iniciar los Servicios
```bash
docker compose up -d
```

## 9. Ejecutar las Migraciones
```bash
docker compose exec backend php artisan migrate --force
```
Este Comando Crea las Tablas Definidas por las Migraciones de Laravel.

## 10. Verificar el Funcionamiento
Comprobar el Estado de los Contenedores:
```bash
docker compose ps
```

Consultar los Registros si algún Servicio presenta problemas:
```bash
docker compose logs --tail=100
```

Comprobar el Estado de las Migraciones:
```bash
docker compose exec backend php artisan migrate:status
```

## 11. Acceso a los Servicios
| Servicio | Dirección |
|---|---|
| Backend Laravel | http://localhost:8000 |
| Frontend React | http://localhost:8080 |
| Mailpit - bandeja de correo | http://localhost:8025 |
| MariaDB | localhost:3307 |

Los Puertos pueden Cambiar si se Modifican las Variables correspondientes en el `.env` de la Raíz.

MariaDB Escucha en el Puerto `3306` Dentro de la Red de Docker. Los demás Contenedores deben utilizar el Nombre de Servicio `mariadb` para Conectarse.

Mailpit recibe Correo SMTP en el Puerto `1025` Dentro de la Red de Docker.

## 12. Detener los Servicios
Para Detener los Contenedores:
```bash
docker compose down
```

El Volumen Externo de MariaDB NO se Elimina con este Comando, por lo que los Datos Persisten.

No Utilizar `docker compose down -v` ni Eliminar el Volumen de MariaDB si se desea Conservar la Información Almacenada.

## 13. Seguridad y Publicación
- NO Publicar `.env`, `Backend/.env`, Claves de Aplicación ni Contraseñas Reales.
- NO Incluir Directorios de Dependencias o Compilaciones Generadas, como `Backend/vendor`, `Frontend/node_modules` o `Frontend/dist`.
- Publicar los Archivos de Plantilla `.env.example`.
- Verificar que el Repositorio de GitHub tenga Permisos de Lectura.
