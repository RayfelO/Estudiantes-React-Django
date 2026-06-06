---
title: "Modulo Core"
date: 2026-06-05
tags: [proyecto, sga, modulo]
aliases:
  - Sistema de Gestion Academica
---

# Modulo Core

Responsabilidad principal: Contener los modelos de dominio, serializadores, permisos custom y la configuracion de autenticacion centralizada. Es la app base sobre la cual se apoyan todas las demas.

## Componentes Clave

### Modelos de Dominio
- `User`: Modelo de usuario custom extendiendo `AbstractUser`. Incluye tipo de documento, numero de documento, correo, fecha de nacimiento. El `USERNAME_FIELD` es `username`.
- `DocumentType`: Catalogo de tipos de documento (Cedula, Pasaporte, etc.).
- `Career`: Carreras universitarias con codigo y nombre.
- `Student`: Perfil de estudiante vinculado 1:1 a `User`. Contiene carrera, anio de ingreso, ciclo de ingreso, GPA general y ultimo GPA.
- `Professor`: Perfil de profesor vinculado 1:1 a `User`. Solo contiene anio de ingreso.
- `Subject`: Asignaturas con codigo, nombre, flag de laboratorio, creditos y profesor asignado.
- `AcademicCycle`: Ciclo academico de un estudiante (anio, ciclo, creditos tomados, GPA).
- `SubjectCycle`: Registro de una asignatura dentro de un ciclo academico. Contiene nota de medio ciclo, nota final, letra final y logica de retiro (letra "R"). Posee restriccion `unique_together` sobre `(cycle, subject)`.
- `Message`: Mensajeria interna entre usuarios (sender, receiver, body, sent_at).

### Serializadores
- `StudentTokenObtainPairSerializer` / `ProfessorTokenObtainPairSerializer`: Serializadores de login JWT que inyectan claims custom (`is_student`, `is_professor`, `profile_id`).
- `StudentSerializer`, `ProfessorSerializer`, `UserSerializer`: Serializacion basica de perfiles.
- `StudentGradesSerializer`, `StudentAcademicRecordSerializer`: Vista de calificaciones y ciclos desde la perspectiva del estudiante.
- `ProfessorViewSerializer`, `ProfessorGradeSerializer`, `ProfessorGradeReviewSerializer`: Vista y mutacion de calificaciones desde la perspectiva del profesor.
- `CycleSubjectsSerializer`: Seleccion de materias por ciclo.
- `PasswordResetSerializer`: Recuperacion de contrasena via email.

### Permisos Custom
- `IsStudent`: Valida que el usuario autenticado tenga un perfil `Student` relacionado.
- `IsProfessor`: Valida que el usuario autenticado tenga un perfil `Professor` relacionado.
- `ReadOnlyProfessor`: Permite solo metodos GET/HEAD/OPTIONS a usuarios con perfil de profesor. Usado para que profesores vean listados de estudiantes.

## Dependencias
- Utilizado por [[Modulo Estudiante]], [[Modulo Profesor]], [[Modulo Ciclos Academicos]] y [[Modulo Asignaturas por Ciclo]] para acceso a modelos y permisos.
- Define `AUTH_USER_MODEL = "core.User"` en settings.

## Observaciones
- No hay logica de negocio compleja en este modulo; es puramente infrastructura de datos y autenticacion.
- Los modelos tienen validadores `MinValueValidator` y `MaxValueValidator` directamente en el schema.
