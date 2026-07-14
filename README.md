# Code-Server Docker Environment

Un entorno de desarrollo basado en **code-server** (VS Code en el navegador) ejecutado dentro de un contenedor Docker, con soporte para Docker-in-Docker (DinD) y montaje de volúmenes personalizados.

## 🚀 Características

- **VS Code en el navegador** - Accede a un entorno de desarrollo completo desde cualquier navegador.
- **Soporte Docker** - Puedes ejecutar comandos Docker dentro del contenedor gracias al montaje de `/var/run/docker.sock`.
- **Persistencia de datos** - Configuración y archivos guardados en volúmenes locales.
- **Personalizable** - Variables de entorno para contraseñas, usuario, zona horaria y más.
- **Extensiones Docker** - Incluye el mod `linuxserver/mods:universal-docker` para funcionalidades extra.

## 📦 Requisitos

- Docker y Docker Compose instalados.
- Puertos disponibles para la asignación (configurables).
- Conocimiento básico de variables de entorno.

## 🛠️ Configuración

### 1. Clona el repositorio

```bash
git clone https://github.com/MartinCiro/code-server.git
cd code-server
```

### 2. Configura variables de entorno

Crea un archivo `.env` en la raíz del proyecto basado en el ejemplo:

```bash
cp .env.example .env
```

Edita el archivo `.env` con tus valores:

```env
PASSWORD=tu_contraseña_segura
FOLDER=/ruta/a/tus/archivos
PORT=8443  # opcional (8443 por defecto)
PUID=1000  # opcional
PGID=1000  # opcional
TZ=Europe/Madrid  # opcional
```

### 3. Archivo `docker-compose.yml`

El servicio está definido de la siguiente manera:

```yaml
services:
  code-server:
    image: lscr.io/linuxserver/code-server:latest
    container_name: docker-code
    environment:
      - PUID=${PUID:-1000}
      - PGID=${PGID:-1000}
      - TZ=${TZ:-Europe/Madrid}
      - PASSWORD=${PASSWORD}
      - HASHED_PASSWORD=${HASHED_PASSWORD:-}
      - SUDO_PASSWORD=${SUDO_PASSWORD:-${PASSWORD}}
      - SUDO_PASSWORD_HASH=${SUDO_PASSWORD_HASH:-}
      - DEFAULT_WORKSPACE=${DEFAULT_WORKSPACE:-/config/workspace}
      - DOCKER_MODS=${DOCKER_MODS:-"linuxserver/mods:universal-docker"}
    volumes:
      - ./config:/config
      - /var/run/docker.sock:/var/run/docker.sock
      - ${FOLDER}:/data
    ports:
      - "${PORT:-8443}:${PORT:-8443}"
```

## 🔧 Variables de entorno

### Obligatorias
| Variable | Descripción | Ejemplo |
|----------|-------------|---------|
| `PASSWORD` | Contraseña de acceso a code-server | `"MiClaveSegura123"` |
| `FOLDER` | Ruta local a montar dentro del contenedor en `/data` | `/home/usuario/proyectos` |

### Opcionales (con valores por defecto)
| Variable | Valor por defecto | Descripción |
|----------|-------------------|-------------|
| `PORT` | `8443` | Puerto de acceso a code-server |
| `PUID` | `1000` | ID de usuario para permisos |
| `PGID` | `1000` | ID de grupo para permisos |
| `TZ` | `Europe/Madrid` | Zona horaria |
| `HASHED_PASSWORD` | *(vacío)* | Contraseña hasheada (reemplaza a PASSWORD si se define) |
| `SUDO_PASSWORD` | `${PASSWORD}` | Contraseña para sudo dentro del contenedor |
| `SUDO_PASSWORD_HASH` | *(vacío)* | Hash para sudo (similar a HASHED_PASSWORD) |
| `DEFAULT_WORKSPACE` | `/config/workspace` | Directorio de trabajo por defecto al abrir code-server |
| `DOCKER_MODS` | `"linuxserver/mods:universal-docker"` | Mods adicionales de LinuxServer.io |

## 🏃 Uso

### Iniciar el contenedor

```bash
docker-compose up -d
```

### Acceder a code-server

Abre tu navegador y ve a:

```
http://localhost:${PORT:-8443}
```

Inicia sesión con la contraseña establecida en `PASSWORD`.

### Comandos útiles

```bash
# Ver logs del contenedor
docker-compose logs -f

# Detener el contenedor
docker-compose down

# Reiniciar el contenedor
docker-compose restart

# Acceder a la terminal del contenedor
docker exec -it docker-code /bin/bash
```

## 🔧 Personalización

### Cambiar el puerto
Define `PORT` en tu archivo `.env` o modifica el valor en `docker-compose.yml`.

### Usar contraseña hasheada
Genera un hash SHA-256 y úsalo en `HASHED_PASSWORD` en lugar de `PASSWORD`:
```bash
echo -n "tu_password" | sha256sum
```

### Agregar más volúmenes
Puedes añadir más volúmenes en la sección `volumes` del `docker-compose.yml` para montar directorios adicionales.

## 🐳 Docker-in-Docker (DinD)

El contenedor tiene acceso al demonio Docker del host, lo que te permite ejecutar comandos Docker desde dentro de code-server. **Nota**: Esto puede representar un riesgo de seguridad si no se maneja adecuadamente.

## ⚠️ Consideraciones de seguridad

- **Contraseñas**: Usa contraseñas seguras y evita valores por defecto.
- **Docker.sock**: El montaje de `/var/run/docker.sock` permite control total sobre Docker del host, úsalo con precaución.
- **Acceso**: Asegura que el puerto no esté expuesto públicamente sin autenticación adicional.

## 📄 Licencia

Este proyecto usa la imagen oficial de [LinuxServer.io](https://docs.linuxserver.io/images/docker-code-server) y está bajo la misma licencia que el proyecto original.

## 🤝 Contribuciones

Las contribuciones son bienvenidas. Por favor, abre un issue o pull request para sugerencias o mejoras.

## 📞 Soporte

- Documentación oficial de code-server: [https://coder.com/docs/code-server](https://coder.com/docs/code-server)
- LinuxServer.io Docs: [https://docs.linuxserver.io/](https://docs.linuxserver.io/)
```