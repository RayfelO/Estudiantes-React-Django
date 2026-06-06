---
title: "Modulo Asignaturas por Ciclo"
date: 2026-06-05
tags: [proyecto, sga, modulo]
aliases:
  - Sistema de Gestion Academica
---

# Modulo Asignaturas por Ciclo

Responsabilidad principal: Gestionar el ciclo de vida de una asignatura dentro de un ciclo academico ya registrado, incluyendo la eliminacion completa del registro y el retiro parcial (letra "R").

## Componentes Clave

### Modelos Relacionados (en [[Modulo Core]])
- `SubjectCycle`: Almacena `midterm_grade`, `final_grade` y `final_grade_letter`. Posee logica custom en `delete()` para restar los creditos del ciclo padre al eliminarse.

### Vistas (APIView)
- `CycleSubjectListView`: DELETE de una asignatura de un ciclo. Requiere `IsStudent`.
  - Valida que la asignatura pertenezca al estudiante logueado.
  - Al eliminar, el metodo `delete()` del modelo resta los creditos de `taken_credits` del ciclo academico.
- `CycleSubjectDetailView`: DELETE que no elimina el registro fisico, sino que marca la calificacion con letra "R" (Retirado). Requiere `IsStudent`.
  - Utilizado para el retiro formal de una materia sin perder el historico.

## Dependencias
- Depende de [[Modulo Core]] para modelos (`SubjectCycle`) y permisos (`IsStudent`).
- Complementa a [[Modulo Ciclos Academicos]]: aquel registra, este elimina o retira.

## Flujo Operativo

### Eliminacion de materia
1. Estudiante accede a su lista de materias inscritas.
2. Solicita eliminar una materia.
3. Se envia DELETE a `/api/v1/subject-cycles/{id}/`.
4. El registro se elimina fisicamente y los creditos se devuelven al ciclo.

### Retiro de materia
1. Estudiante solicita retirar una materia despues del periodo de eliminacion.
2. Se envia DELETE a `/api/v1/subject-cycles/{id}/detail/`.
3. La calificacion final se establece en "R" sin eliminar el registro.

## Observaciones
- La letra "R" no esta definida en las choices de `final_grade_letter`, lo que podria generar inconsistencias si se intenta serializar o validar contra el schema.
- No existe endpoint PUT/PATCH para actualizar calificaciones desde la perspectiva del estudiante; solo el profesor puede modificar notas.
