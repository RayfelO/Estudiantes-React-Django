---
title: "Flujo del Profesor"
date: 2026-06-05
tags: [proyecto, sga, flujo]
aliases:
  - Sistema de Gestion Academica
---

# Flujo del Profesor

Flujo operativo que un profesor sigue dentro del sistema: desde el dashboard hasta la asignacion y revision de calificaciones de sus estudiantes.

## Paso a Paso

1. **Dashboard del profesor**
   - Tras el login, el profesor llega a `/dashboard-profesor`.
   - La pagina muestra un resumen de sus asignaturas y accesos rapidos.

2. **Listado de asignaturas**
   - El profesor consulta sus asignaturas. El frontend obtiene el listado general de `/api/v1/professor/subjects/` ([[Modulo API Profesor]]).
   - Este endpoint retorna todas las asignaturas con su profesor asignado, sin filtrar por el profesor logueado (endpoint publico).

3. **Ver estudiantes inscritos**
   - Al seleccionar una asignatura, el frontend solicita `/api/v1/professor/subjects/{id}/students/` ([[Modulo API Profesor]]).
   - El backend filtra las `SubjectCycle` donde la asignatura pertenece al profesor logueado.
   - Retorna lista de estudiantes con sus calificaciones actuales.

4. **Asignar calificacion inicial**
   - El profesor ingresa `midterm_grade` (hasta 70) y/o `final_grade` (hasta 100).
   - El frontend envia POST a `/api/v1/professor/subjects/{id}/grade/` ([[Modulo API Profesor]]).
   - El backend valida que la nota actual sea `0`; si ya tiene valor, rechaza la escritura.
   - Esto previene sobrescrituras accidentales.

5. **Revision y correccion de calificacion**
   - Si el profesor necesita corregir una calificacion ya asignada, usa el endpoint de revision.
   - El frontend envia PUT a `/api/v1/professor/subjects/{id}/grade/review` ([[Modulo API Profesor]]).
   - Este endpoint permite modificar `final_grade` sin la restriccion de `0`.
   - Se asume que el calculo de la letra (`A`, `B`, `C`, `D`, `F`) ocurre automaticamente o se deriva del `final_grade`.

## Entidades y Estados Importantes

- `Subject`: Tiene un profesor asignado via `ForeignKey`. Una asignatura pertenece a un unico profesor.
- `SubjectCycle`: Estado de la calificacion. Campos `midterm_grade` (0-70) y `final_grade` (0-100).
- Restriccion de escritura: `midterm_grade` y `final_grade` solo pueden escribirse una vez via el endpoint de registro inicial. Correcciones requieren el endpoint de revision.

## Modulos Involucrados
- [[Modulo API Profesor]] (perfil, listado de asignaturas, vistas de estudiantes, registro y revision de calificaciones).
- [[Modulo Core]] (modelos `Subject`, `SubjectCycle`, `Professor`).
- [[Modulo Frontend]] (`DashboardProfesor`, tablas de edicion).

## Diagrama Mental

```
Login -> Dashboard Profesor
            |
            v
        Asignaturas (GET /subjects/)
            |
            v
        Estudiantes (GET /subjects/{id}/students/)
            |
    +-------+-------+
    |               |
 Registrar       Revisar
  Notas          Notas
  (POST)         (PUT)
   |               |
   v               v
  Validar:        Sin validar:
  grade == 0      grade cualquiera
```
