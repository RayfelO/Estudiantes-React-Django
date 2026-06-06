---
title: "Flujo del Estudiante"
date: 2026-06-05
tags: [proyecto, sga, flujo]
aliases:
  - Sistema de Gestion Academica
---

# Flujo del Estudiante

Flujo operativo completo que un estudiante sigue dentro del sistema: desde el dashboard hasta la consulta de calificaciones y el retiro de materias.

## Paso a Paso

1. **Dashboard del estudiante**
   - Tras el login, el estudiante llega a `/dashboard-estudiante2`.
   - La pagina muestra un resumen academico (GPA, ciclos, alertas).
   - Desde aqui puede navegar a: Perfil, Seleccion de materias, Materias inscritas, Calificaciones.

2. **Seleccion de materias**
   - El estudiante accede al wizard de seleccion (`SeleccionEstudiante` -> `SeleccionEstudiante1` -> `SeleccionEstudiante2`).
   - El frontend muestra las asignaturas disponibles obtenidas de [[Modulo API Profesor]] (`SubjectList`).
   - Al confirmar, el frontend envia un POST a `/api/v1/academic-cycles/{id}/` ([[Modulo Ciclos Academicos]]).
   - El backend valida que la asignatura no este duplicada en ese ciclo (`IntegrityError` -> HTTP 409).
   - Si es exitoso, la materia queda registrada en `SubjectCycle` vinculada al `AcademicCycle` del estudiante.

3. **Consulta de materias inscritas**
   - En `/materias-estudiante`, el estudiante ve sus `SubjectCycle` activas.
   - Puede optar por eliminar una materia (DELETE a `/api/v1/subject-cycles/{id}/`) o retirarla (DELETE a `/api/v1/subject-cycles/{id}/detail/` que marca letra "R"). [[Modulo Asignaturas por Ciclo]].

4. **Consulta de calificaciones**
   - En `/calificaciones-estudiante`, el frontend solicita las calificaciones a `/api/v1/students/profile/grades/` ([[Modulo API Estudiante]]).
   - El backend retorna todas las `SubjectCycle` del estudiante con `midterm_grade`, `final_grade` y `final_grade_letter`.
   - El frontend puede visualizar graficos usando `chart.js`.

5. **Ver perfil y record academico**
   - En `/ver-perfil`, el estudiante ve sus datos personales obtenidos de `/api/v1/students/profile/`.
   - Puede acceder a su record academico completo via `/api/v1/students/profile/academic-record/`, que lista todos sus `AcademicCycle` historicos.

## Entidades y Estados Importantes

- `AcademicCycle`: Representa un periodo academico del estudiante. Puede tener multiples `SubjectCycle`.
- `SubjectCycle`: Estado de una materia en un ciclo. Puede estar en curso, calificada, o retirada ("R").
- `overall_gpa` / `last_gpa`: Campos del perfil del estudiante. Se asume que se actualizan fuera de banda o manualmente.

## Modulos Involucrados
- [[Modulo API Estudiante]] (perfil, calificaciones, record academico).
- [[Modulo Ciclos Academicos]] (registro de materias).
- [[Modulo Asignaturas por Ciclo]] (eliminacion y retiro).
- [[Modulo API Profesor]] (listado de asignaturas disponibles).
- [[Modulo Frontend]] ( todas las paginas del flujo estudiante).

## Diagrama Mental

```
Login -> Dashboard Estudiante
            |
    +-------+-------+-------+-------+
    |       |       |       |       |
 Perfil  Seleccion  Materias  Califs  Record
            |       |       |
            v       v       v
        Wizard    CRUD    GET
        POST     DELETE   Grades
```
