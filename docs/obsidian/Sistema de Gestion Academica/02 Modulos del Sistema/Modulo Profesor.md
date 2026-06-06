---
title: "Modulo Profesor"
date: 2026-06-05
tags: [proyecto, sga, modulo]
aliases:
  - Sistema de Gestion Academica
---

# Modulo Profesor

Responsabilidad principal: Exponer endpoints REST para que el actor profesor pueda autenticarse, consultar su perfil, listar sus asignaturas, ver estudiantes inscritos, asignar calificaciones (inicial y revision) y ver el listado general de asignaturas.

## Componentes Clave

### Vistas (APIView)
- `ProfessorLoginView`: Hereda de `TokenObtainPairView`. Usa `ProfessorTokenObtainPairSerializer` para emitir JWT con claim `is_professor: true`.
- `ProfessorProfileView`: GET del perfil del profesor autenticado. Requiere `IsProfessor`.
- `SubjectList`: GET de todas las asignaturas con su profesor asignado (`SubjectWithProfessorSerializer`). No requiere autenticacion.
- `ProfessorSubjectViewStudent`: GET de los estudiantes inscritos en una asignatura especifica filtrada por el profesor logueado. Requiere `IsProfessor`.
- `ProfessorSubjectGradeStudent`: POST para registrar o actualizar la calificacion de un estudiante en una asignatura (`midterm_grade` y/o `final_grade`). Solo permite escritura si el campo actual es `0`. Requiere `IsProfessor`.
- `ProfessorSubjectEditGradeStudent`: PUT para editar/revisar la calificacion final de un estudiante. No tiene la restriccion de `0` del endpoint anterior. Requiere `IsProfessor`.
- `ProfessorListView` / `ProfessorDetailView`: Endpoints de listado y detalle de profesores (comentados en `urls.py`, pero existen). Requieren `ReadOnlyProfessor`.

## Dependencias
- Depende de [[Modulo Core]] para modelos (`Professor`, `Subject`, `SubjectCycle`), serializadores y permisos.
- El frontend usa el claim `is_professor` del JWT para redirigir al dashboard de profesor.

## Tecnologias Especificas
- Django REST Framework `APIView`.
- `rest_framework_simplejwt` para login.
- Permisos custom de [[Modulo Core]].

## Observaciones
- La logica de asignacion de notas (`ProfessorSubjectGradeStudent`) tiene una regla de negocio implicita: si la nota ya fue asignada (diferente de 0), no se permite sobrescribir. Esto obliga a usar el endpoint de revision (`ProfessorSubjectEditGradeStudent`) para correcciones.
- El `SubjectList` no tiene restriccion de permisos, por lo que cualquiera puede ver las asignaturas disponibles.
- Algunas vistas tienen errores de tipografia en el nombre de atributos (`serializers_class` en lugar de `serializer_class`), lo cual funciona por coincidencia de nombres en Python pero no es consistente.
