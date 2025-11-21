# UrBackup – Contenedor Docker

Este repositorio contiene la configuración necesaria para desplegar un servidor **UrBackup** utilizando **Docker Compose**.  
UrBackup es una solución de backup cliente/servidor que permite realizar copias de seguridad de archivos e imágenes del sistema de manera eficiente y centralizada.

---

## 🚀 Características del contenedor

- **Imagen oficial:** `uroni/urbackup-server:latest`
- Persistencia de datos (base de datos + backups)
- Configuración sencilla mediante variables de entorno
- Puertos necesarios expuestos para la comunicación cliente-servidor
- Reinicio automático (`unless-stopped`)

---

## 📁 Estructura de archivos

```text
.
├── compose.yml
├── database/
└── backup/
```

- `database/` → contiene la base de datos interna del servidor UrBackup  
- `backup/` → almacena todas las copias de seguridad realizadas  

> **Importante:** asegúrate de que tu usuario tenga permisos de lectura/escritura sobre estos directorios.

---

## 🐳 Archivo de Docker Compose (`compose.yml`)

```yaml
services:
  urbackup:
    image: uroni/urbackup-server:latest
    container_name: urbackup
    restart: unless-stopped
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Europe/Madrid
    ports:
      - "55413:55413"
      - "55414:55414"
      - "55415:55415"
      - "35623:35623/udp"
    volumes:
      - ./database:/var/urbackup
      - ./backup:/backups
```

---

## 🔧 Variables de entorno

| Variable | Descripción                                                  |
|----------|--------------------------------------------------------------|
| `PUID`   | ID de usuario que ejecutará el proceso dentro del contenedor |
| `PGID`   | ID de grupo asociado                                         |
| `TZ`     | Zona horaria                                                 |

Puedes comprobar tu UID y GID con:

```bash
id $USER
```

---

## 🌐 Puertos utilizados

| Puerto Host | Puerto Contenedor | Protocolo | Uso                            |
|-------------|-------------------|-----------|--------------------------------|
| 55413       | 55413             | TCP       | Interfaz web y API             |
| 55414       | 55414             | TCP       | Comunicación cliente-servidor  |
| 55415       | 55415             | TCP       | Servicio adicional UrBackup    |
| 35623       | 35623             | UDP       | Descubrimiento de clientes     |

---

## ▶️ Puesta en marcha

### 1️⃣ Clona el repositorio

```bash
git clone https://github.com/tuusuario/tu-repo-urbackup.git
cd tu-repo-urbackup
```

### 2️⃣ Crea los directorios persistentes

```bash
mkdir database backup
```

### 3️⃣ Arranca el contenedor

```bash
docker compose up -d
```

### 4️⃣ Accede a la interfaz web

Abre en tu navegador:

```text
http://TU-IP:55414
```

---

## 🛑 Detener el contenedor

```bash
docker compose down
```

---

## 🔄 Actualizar a la última versión

```bash
docker compose pull
docker compose up -d
```

