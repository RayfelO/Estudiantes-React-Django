---
title: "Configuracion Local de Desarrollo"
date: 2026-06-05
tags: [proyecto, sga, despliegue]
aliases:
  - Sistema de Gestion Academica
---

# Configuracion Local de Desarrollo

## Ambiente de Desarrollo

El proyecto esta configurado para ejecutarse completamente en local sin dependencias de servicios externos (salvo la base de datos, que puede ser SQLite segun el engine configurado).

### Backend

| Variable | Valor tipico | Proposito |
|----------|-------------|-----------|
| `SECRET_KEY` | string aleatorio | Clave de firma de Django y JWT |
| `DEBUG` | `True` | Modo debug con trazas detalladas |
| `ALLOWED_HOSTS` | `127.0.0.1,localhost` | Hosts permitidos |
| `DATABASE_ENGINE` | `django.db.backends.sqlite3` | Motor de base de datos |
| `DATABASE_NAME` | `db.sqlite3` | Nombre del archivo o base de datos |
| `CORS_ALLOWED_ORIGINS` | `http://localhost:3000` | Origen del frontend React |
| `CSRF_TRUSTED_ORIGINS` | `http://localhost:3000` | Origen confiable para CSRF |

### Frontend

- Puerto por defecto: `3000` (Create React App).
- Proxy configurado implicitamente hacia `http://127.0.0.1:8000` via axios.

### URLs Relevantes en Local

| Servicio | URL |
|----------|-----|
| Frontend | `http://localhost:3000/` |
| Backend API | `http://127.0.0.1:8000/` |
| Admin Django | `http://127.0.0.1:8000/admin/` |
| Swagger UI | `http://127.0.0.1:8000/api/v1/schema/swagger-ui/` |
| Esquema OpenAPI | `http://127.0.0.1:8000/api/v1/schema/` |

## Pipeline de Desarrollo

No existe CI/CD configurado. El flujo de trabajo es manual:

1. Clonar el repositorio.
2. Crear entorno virtual Python e instalar dependencias (`requirements.txt`).
3. Aplicar migraciones (`migrate`).
4. Opcional: cargar fixtures (`loaddata`) para datos de prueba.
5. Ejecutar `python manage.py runserver` en una terminal.
6. En otra terminal, ejecutar `npm install` y `npm start` en `frontend/`.

## Consideraciones

- No hay contenedores Docker ni configuracion de produccion.
- El modo `DEBUG=True` expone informacion sensible si se ejecuta en red publica.
- Los tokens JWT tienen una vida util de 180 dias, lo cual es seguro para desarrollo local pero inseguro para cualquier otro ambiente.
- No esta configurado el envio de emails real para la recuperacion de contrasena; el endpoint `PasswordResetView` actualiza la contrasena directamente sin enviar un link de reseteo.
