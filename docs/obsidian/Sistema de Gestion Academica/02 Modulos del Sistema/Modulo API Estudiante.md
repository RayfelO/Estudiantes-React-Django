---
title: "Modulo API Estudiante"
date: 2026-06-05
tags: [proyecto, sga, modulo]
aliases:
  - Sistema de Gestion Academica
---

# Modulo API Estudiante

Responsabilidad principal: Exponer endpoints REST para que el actor estudiante pueda autenticarse, consultar su perfil, ver calificaciones, ver su record academico y resetear su contrasena.

## Componentes Clave

### Vistas (APIView)
- `StudentLoginView`: Hereda de `TokenObtainPairView`. Usa `StudentTokenObtainPairSerializer` para emitir JWT con claim `is_student: true`.
- `StudentProfileView`: GET del perfil del estudiante autenticado. Requiere permiso `IsStudent`.
- `StudentProfileGradesView`: GET de todas las calificaciones del estudiante (`SubjectCycle` filtradas por el usuario logueado). Requiere `IsStudent`.
- `StudentProfileAcademicRecordView`: GET de todos los ciclos academicos del estudiante (`AcademicCycle` filtrados por usuario). Requiere `IsStudent`.
- `PasswordResetView`: POST para actualizar la contrasena buscando por email. No requiere autenticacion.
- `StudentListView` / `StudentDetailView`: Endpoints de listado y detalle de estudiantes (comentados en `urls.py`, pero existen en codigo). Requieren `ReadOnlyProfessor`.

## Dependencias
- Depende de [[Modulo Core]] para modelos (`Student`, `SubjectCycle`, `AcademicCycle`), serializadores y permisos.
- El token JWT emitido incluye claims que identifican al usuario como estudiante, usados por el frontend para redireccionamiento de rutas.

## Tecnologias Especificas
- Django REST Framework `APIView`.
- `rest_framework_simplejwt` para login.
- Permisos custom de [[Modulo Core]].

## Observaciones
- Las vistas usan bloques `try/except` generales que retornan `"Something went wrong"` para cualquier error inesperado, dificultando el debug.
- No hay paginacion explicita en estas vistas; confian en la configuracion global de DRF.
