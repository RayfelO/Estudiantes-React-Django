---
title: "Ideas Futuras"
date: 2026-06-05
tags: [proyecto, sga, notas]
aliases:
  - Sistema de Gestion Academica
---

# Ideas Futuras

Mejoras, refactorings e investigaciones pendientes que podrian aplicarse al proyecto si se retomara en algun momento.

## Refactoring del Frontend

- Migrar de componentes pagina-centricos a una arquitectura de componentes reutilizables (atomic design).
- Reemplazar CSS Modules autogenerados por Tailwind CSS o un sistema de diseno consistente.
- Implementar manejo de estado global (Context API o Redux) para evitar prop drilling.
- Agregar lazy loading y code splitting para las rutas.
- Implementar manejo de errores global y toasts/notificaciones.

## Backend

- Mover los modelos desde `core` a sus respectivas apps (`student.models`, `professor.models`) siguiendo convenciones Django.
- Extraer logica de negocio de las vistas (APIView) a servicios o casos de uso.
- Implementar tests unitarios y de integracion (actualmente solo existen archivos `tests.py` vacios).
- Agregar paginacion explicita en endpoints que devuelven listas grandes.
- Reducir el tiempo de vida de los tokens JWT a algo razonable (ej. 15 min access, 7 dias refresh).
- Implementar blacklist de tokens en logout.
- Agregar envio real de emails para recuperacion de contrasena.
- Implementar throttling en la API para prevenir abuso.

## Funcionalidades Nuevas

- Notificaciones push o internas (el modelo `Message` ya existe pero parece no tener endpoints expuestos).
- Horario de clases visual (integrar con `SubjectCycle` y mostrar un calendario).
- Reportes de estadisticas por carrera, profesor o ciclo.
- Modulo de administrador con dashboard propio (no solo el admin de Django).
- Sistema de pre-requisitos para asignaturas.
- Historial de auditoria de cambios de calificaciones (quien cambio que y cuando).

## Migracion a Produccion

- Dockerizar el proyecto (Dockerfile para backend y frontend, docker-compose).
- Configurar PostgreSQL en lugar de SQLite.
- Configurar un reverse proxy (Nginx) y servir el frontend como static files.
- Variables de entorno via `.env` para todos los entornos.
- Configurar HTTPS y certificados SSL.
