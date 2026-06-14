# CEO CRM — Clientes

Panel de administración de clientes y proyectos para **Desarrollo Empresarial CEO**.

---

## Stack

- **Node.js 20** + **Express 4**
- Servidor estático de un solo archivo HTML
- Login con **Google OAuth (GSI)**
- Datos en **MongoDB** via webhooks de **n8n**

---

## Estructura

```
crm-docker/
├── Dockerfile
├── .dockerignore
├── package.json
├── server.js
└── public/
    └── clientes.html
```

---

## Variables de entorno

No requiere variables de entorno. La configuración vive directamente en `clientes.html`:

| Variable | Dónde | Descripción |
|---|---|---|
| `GOOGLE_CLIENT_ID` | `clientes.html` línea ~230 | Client ID de Google OAuth |
| `N8N.*` | `clientes.html` línea ~240 | URLs de webhooks de n8n |
| `ACCESOS` | `clientes.html` línea ~220 | Emails autorizados y roles |

---

## Accesos

| Email | Nombre | Rol |
|---|---|---|
| `adrianvg3107@gmail.com` | Adrian Vera | Admin (editar todo) |
| `scarletit99@gmail.com` | Scarlet Ramirez | Admin (editar todo) |

Cualquier otra cuenta de Google verá **"Acceso denegado"**.

---

## Correr localmente

```bash
# Instalar dependencias
npm install

# Iniciar servidor
npm start

# Abrir en navegador
http://localhost:3000
```

---

## Build y deploy con Docker

```bash
# Construir imagen
docker build -t ceo-crm .

# Correr contenedor
docker run -p 3000:3000 ceo-crm

# Abrir en navegador
http://localhost:3000
```

---

## Deploy en EasyPanel

### Opción A — Subir código directamente

1. En EasyPanel crear nuevo servicio → **App**
2. Subir esta carpeta como fuente
3. EasyPanel detecta el `Dockerfile` y construye automáticamente
4. Configurar dominio y puerto **3000**

### Opción B — Imagen tar.gz

```bash
# Construir y exportar imagen
docker build -t ceo-crm .
docker save ceo-crm | gzip > ceo-crm.tar.gz

# En EasyPanel → Import Image → subir ceo-crm.tar.gz
```

---

## Configurar Google OAuth

1. Ir a [Google Cloud Console](https://console.cloud.google.com)
2. Seleccionar el proyecto **N8Nautomatizacion**
3. APIs y servicios → Credenciales → **Desarrollo Empresarial CEO**
4. En **Orígenes autorizados de JavaScript** agregar el dominio de EasyPanel:
   ```
   https://tu-subdominio.easypanel.host
   ```
5. Guardar (tarda ~5 minutos en propagar)

---

## Conectar n8n

Una vez validada la vista, se necesitan crear estos webhooks en n8n:

| Webhook | Método | Descripción |
|---|---|---|
| `crm-get-clientes` | GET | Retorna todos los clientes |
| `crm-get-proyectos` | GET | Retorna proyectos por `id_cliente` |
| `crm-create-cliente` | POST | Crea un nuevo cliente en MongoDB |
| `crm-update-cliente` | POST | Actualiza campos de un cliente |
| `crm-create-proyecto` | POST | Crea un nuevo proyecto |
| `crm-update-proyecto` | POST | Actualiza campos de un proyecto |

Mientras no estén conectados, el CRM usa **datos mock** de ejemplo.

---

## Puerto

El servidor corre en el puerto **3000** por defecto.  
Se puede cambiar con la variable de entorno `PORT`:

```bash
PORT=8080 node server.js
```

---

## Versión

`v1.0.0` — Junio 2026  
Proyecto: Desarrollo Empresarial CEO
