---
title: "Autenticacion y Autorizacion"
date: 2026-06-05
tags: [proyecto, sga, decision]
aliases:
  - Sistema de Gestion Academica
---

# Autenticacion y Autorizacion

## Problema

El sistema requiere autenticar dos actores distintos (estudiantes y profesores) con flujos de UI separados. Django tradicionalmente usa grupos y permisos basados en el modelo `User`, pero el equipo necesitaba una forma rapida de identificar el tipo de usuario desde el token sin hacer consultas adicionales a la base de datos.

## Opcion Elegida

Usar `django-rest-framework-simplejwt` con serializadores custom (`StudentTokenObtainPairSerializer` y `ProfessorTokenObtainPairSerializer`) que inyectan claims adicionales en el payload del JWT:
- `is_student`: boolean
- `is_professor`: boolean
- `profile_id`: ID del perfil relacionado

El frontend lee estos claims para redirigir al dashboard correcto y el backend usa permisos custom (`IsStudent`, `IsProfessor`) para proteger los endpoints.

## Por Que

- **Stateless**: No requiere sesiones de servidor ni consultas a DB en cada request para verificar el rol.
- **Separacion clara**: Estudiante y profesor tienen endpoints distintos, serializadores distintos y dashboards distintos.
- **Simple de implementar**: SimpleJWT ya viene con hooks para claims custom.

## Opciones Descartadas

- **Django Sessions**: Rechazada porque el frontend es un SPA React que corre en un dominio distinto (CORS), haciendo las sesiones basadas en cookies mas complejas.
- **OAuth2 / OpenID Connect**: Rechazada por complejidad innecesaria para un proyecto academico con solo dos roles internos.
- **Grupos de Django + Permissions nativas**: Rechazada porque el equipo preferia una solucion mas explicita y rapidamente verificable desde el token.

## Impacto en el Sistema

- El frontend debe almacenar y parsear el JWT para extraer claims.
- Los endpoints de login estan duplicados (`/students/login/` y `/professor/login/`) aunque apuntan a la misma tabla `User`.
- Los permisos custom (`IsStudent`, `IsProfessor`, `ReadOnlyProfessor`) se vuelven criticos; si fallan, un estudiante podria acceder a endpoints de profesor y viceversa.
- El tiempo de vida de los tokens es extremadamente largo (180 dias access, 365 dias refresh), lo que reduce la frecuencia de re-login pero incrementa el riesgo de seguridad si un token es robado.
