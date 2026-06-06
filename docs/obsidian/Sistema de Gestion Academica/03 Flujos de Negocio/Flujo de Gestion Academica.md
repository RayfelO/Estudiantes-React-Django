---
title: "Flujo de Gestion Academica"
date: 2026-06-05
tags: [proyecto, sga, flujo]
aliases:
  - Sistema de Gestion Academica
---

# Flujo de Gestion Academica

Flujo operativo de alto nivel que describe como las entidades academicas (carreras, asignaturas, ciclos, calificaciones) se relacionan y evolucionan dentro del sistema. Este flujo es principalmente administrativo y de configuracion.

## Paso a Paso

1. **Configuracion de catalogos**
   - Un administrador accede al panel de Django (`/admin/`).
   - Registra `DocumentType` (tipos de documento), `Career` (carreras) y `Subject` (asignaturas con profesor asignado).
   - Cada asignatura tiene creditos (0-10), flag de laboratorio, y un profesor vinculado.

2. **Creacion de usuarios y perfiles**
   - Se crean usuarios (`User`) desde el admin o via fixtures.
   - Se vincula un perfil `Student` o `Professor` al usuario.
   - Para estudiantes, se define la carrera, anio de ingreso y ciclo de ingreso.
   - Para profesores, solo se define el anio de ingreso.

3. **Apertura de ciclo academico**
   - Se crea un `AcademicCycle` para un estudiante especifico.
   - Se definen anio, ciclo (numero), y se inicializan `taken_credits`, `overall_gpa` y `last_gpa` en 0.

4. **Seleccion de materias por parte del estudiante**
   - El estudiante, desde el frontend, selecciona asignaturas disponibles.
   - El sistema crea registros `SubjectCycle` vinculados al `AcademicCycle`.
   - Se valida que no haya duplicados (`unique_together` en `SubjectCycle`).
   - Se incrementan los `taken_credits` del ciclo.

5. **Registro de calificaciones por parte del profesor**
   - El profesor asignado a la asignatura registra notas en los `SubjectCycle` correspondientes.
   - `midterm_grade` (0-70) y `final_grade` (0-100).
   - El sistema deriva la letra final basada en el rango numerico (A: 90-100, B: 80-89, etc.).

6. **Retiro o eliminacion de materias**
   - El estudiante puede eliminar una materia antes de que finalice el periodo de eliminacion.
   - Elimina el `SubjectCycle` y los creditos se restan del ciclo.
   - Si el periodo de eliminacion ya paso, puede retirar la materia (letra "R").

7. **Generacion de ranking academico**
   - Con base en los `AcademicCycle` y sus `overall_gpa`, el sistema puede generar un ranking de estudiantes.
   - Este ranking se presenta ordenado de mayor a menor GPA.
   - El frontend puede exportar reportes en PDF usando `@react-pdf/renderer`.

## Entidades y Estados Importantes

- `Career` -> `Student` -> `AcademicCycle` -> `SubjectCycle` -> `Subject` -> `Professor`
- Cadena de dependencias que vincula toda la informacion academica.

## Modulos Involucrados
- [[Modulo Core]] (todos los modelos de dominio).
- [[Modulo Ciclos Academicos]] (seleccion de materias).
- [[Modulo Asignaturas por Ciclo]] (retiro/eliminar).
- [[Modulo API Profesor]] (registro de calificaciones).
- [[Modulo Frontend]] (visualizacion, reportes PDF).

## Diagrama Mental

```
Admin Panel
    |
    v
Catalogos (Career, Subject, DocumentType)
    |
    v
Usuarios + Perfiles (Student, Professor)
    |
    v
AcademicCycle (anio, ciclo)
    |
    v
SubjectCycle (materia + notas)
    |
    v
Calificacion (A/B/C/D/F/R)
    |
    v
Ranking + Reportes PDF
```
