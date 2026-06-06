---
title: "Modulo Ciclos Academicos"
date: 2026-06-05
tags: [proyecto, sga, modulo]
aliases:
  - Sistema de Gestion Academica
---

# Modulo Ciclos Academicos

Responsabilidad principal: Gestionar la seleccion de asignaturas por parte del estudiante dentro de un ciclo academico activo. Un ciclo academico representa un periodo (anio + ciclo) en la vida del estudiante.

## Componentes Clave

### Modelos Relacionados (en [[Modulo Core]])
- `AcademicCycle`: Contiene anio, ciclo, estudiante, creditos tomados, GPA general y ultimo GPA.
- `SubjectCycle`: Vincula una asignatura con un ciclo academico y almacena las calificaciones.

### Vistas (APIView)
- `CycleSubjectsView`: Permite a un estudiante autenticado (`IsStudent`) registrar una asignatura en uno de sus ciclos academicos via POST.
  - Valida que el ciclo pertenezca al estudiante logueado.
  - Maneja `IntegrityError` cuando la asignatura ya fue seleccionada en ese ciclo (HTTP 409).
  - Actualiza automaticamente `taken_credits` del ciclo al agregar una materia.

## Dependencias
- Depende de [[Modulo Core]] para modelos (`AcademicCycle`, `SubjectCycle`) y serializadores (`CycleSubjectsSerializer`).
- Utilizado por el [[Modulo Frontend]] en la vista de seleccion de materias del estudiante.

## Flujo Operativo
1. El estudiante navega a su dashboard y accede a la seleccion de materias.
2. El frontend muestra las asignaturas disponibles.
3. Al confirmar la seleccion, se envia un POST a `/api/v1/academic-cycles/{id}/` con la asignatura elegida.
4. El backend valida que no exista duplicado y registra la materia en el ciclo.

## Observaciones
- No existe endpoint de eliminacion de ciclo academico en este modulo; el retiro de materias se maneja en [[Modulo Asignaturas por Ciclo]].
- La logica de calculo de GPA no esta centralizada en este modulo; los campos `overall_gpa` y `last_gpa` parecen ser campos de proyeccion o snapshot.
