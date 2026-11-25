# 🐳 Homelab Docker & Local AI Infrastructure

Arquitectura de infraestructura **self-hosted** desplegada sobre **Ubuntu Server**, diseñada bajo principios de aislamiento de servicios, automatización con **Docker Compose**, seguridad en la gestión de credenciales (**Docker Secrets**) y acceso remoto cifrado mediante **Tailscale**.

---

## 🏗️ Arquitectura General

Cada servicio se ejecuta de forma aislada dentro de su propio directorio y definición docker-compose.yml, facilitando el mantenimiento, las actualizaciones independientes y la portabilidad del entorno:

* **Acceso remoto seguro (Zero-Trust):** Toda la administración y consumo de servicios desde el exterior se realiza a través de una red privada **Tailnet (Tailscale)**, evitando exponer puertos innecesarios directamente a Internet.
* **IA Local & Desarrollo Asistido:** Despliegue local de modelos LLM mediante **Ollama**, conectados tanto a interfaces conversacionales (**Open WebUI**) como a entornos de programación agéntica (**OpenCode**).
* **Reverse Proxy:** Gestión centralizada de certificados y enrutamiento interno mediante **Nginx Proxy Manager (NPM)**.

---

## 📦 Servicios Desplegados

| Servicio / Carpeta | Tecnología / Imagen Principal | Función |
| :--- | :--- | :--- |
| **`ollama`** | Ollama | Motor de inferencia local para ejecución de modelos LLM en servidor propio. |
| **`open-webui`** | Open WebUI | Interfaz web conversacional conectada a instancias locales de Ollama. |
| **`opencode`** | OpenCode Server | Agente de programación en local integrado con el workspace y modelos Ollama. |
| **`npm`** | Nginx Proxy Manager | Proxy inverso y gestión de certificados SSL/TLS para servicios internos. |
| **`mi-uptime-kuma`** | Uptime Kuma *(Custom Build)* | Versión personalizada para monitorización de disponibilidad y alertas de servicios. |
| **`arcane`** | Arcane *(Custom Build)* | Panel de gestión y administración visual de contenedores Docker. |
| **`homepage`** | Homepage | Dashboard centralizado de acceso rápido y métricas del servidor. |
| **`vaultwarden`** | Vaultwarden | Gestor de contraseñas autoalojado compatible con Bitwarden. |
| **`paperless-ngx`** | Paperless-ngx | Sistema de gestión documental, OCR e indexación inteligente de archivos. |
| **`homeassistant`** | Home Assistant *(Custom Build)* | Plataforma de domótica y automatización del hogar. |
| **`komga`** | Komga | Servidor multimedia para gestión y lectura de cómics y libros digitales. |
| **`syncthing`** | Syncthing | Sincronización continua y descentralizada de archivos entre dispositivos. |
| **`qbittorrent`** | qBittorrent | Cliente de descargas P2P con interfaz web. |
| **`notediscovery`** | NoteDiscovery | Base de conocimiento y gestión de notas en Markdown. |
| **`PHP-Dynamic-Qr-code`** | PHP / Servidor Web | Aplicación web propia para generación y gestión de códigos QR dinámicos. |
| **`servidor-web`** | Stack Web *(En desarrollo)* | Entorno de desarrollo y pruebas para aplicaciones web propias. |

---

## 🔒 Seguridad y Gestión de Secretos (DevSecOps)

Para evitar la exposición de contraseñas, tokens o claves API en el repositorio o mediante comandos como `docker inspect`:

1. **Externalización de Variables (`.env`):** Ningún `docker-compose.yml` contiene credenciales codificadas (*hardcoded*). Todos los parámetros sensibles se inyectan desde archivos `.env` excluidos del control de versiones mediante `.gitignore`.
2. **Patrón Docker Secrets (`_FILE`):** Siempre que la imagen lo soporta, se utiliza el sufijo `*_FILE` (por ejemplo, `DB_PASSWORD_FILE`) apuntando a archivos de secretos montados en solo lectura dentro del contenedor, evitando que las contraseñas viajen en variables de entorno en memoria.
3. **Permisos Restrictivos en Host:** Los directorios de credenciales cuentan con permisos estrictos (`700` para carpetas y `600` para archivos de secretos).
4. **Plantillas de Configuración (`credentials/`):** En la carpeta `credentials/` se incluyen únicamente plantillas seguras (`*.env.example` y `*.txt.example`) como referencia de despliegue.

---

## 📂 Estructura del Repositorio

```text
homelab-docker-architecture/
├── credentials/          # Plantillas de ejemplo (.env.example y secretos _FILE)
├── ollama/               # Servicio de modelos LLM locales
├── open-webui/           # Interfaz web para IA local
├── opencode/             # Entorno de desarrollo asistido por IA
├── vaultwarden/          # Gestión de contraseñas
├── paperless-ngx/        # Gestión documental con OCR
├── ...                   # Resto de servicios aislados por carpeta
├── .gitignore            # Reglas estrictas de exclusión de secretos y volúmenes
└── README.md             # Documentación de la arquitectura
```

---

## 🚀 Despliegue Rápido de un Servicio

1. Clonar el repositorio:
   ```bash
   git clone https://github.com/CesarPC-goroban/homelab-docker-architecture.git
   cd homelab-docker-architecture
   ```
2. Copiar las plantillas de entorno y secretos desde `credentials/` y rellenar con valores reales (asegurando permisos `chmod 600`).
3. Levantar el servicio deseado de forma independiente:
   ```bash
   cd ollama
   docker compose up -d
   ```
