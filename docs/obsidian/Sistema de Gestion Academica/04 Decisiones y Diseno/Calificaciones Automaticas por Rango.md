---
title: "Calificaciones Automaticas por Rango"
date: 2026-06-05
tags: [proyecto, sga, decision]
aliases:
  - Sistema de Gestion Academica
---

# Calificaciones Automaticas por Rango

## Problema

El sistema necesita registrar calificaciones numericas pero tambien mostrarlas en formato de letra (A, B, C, D, F) segun un rango establecido. La pregunta era: donde y cuando realizar la conversion.

## Opcion Elegida

Almacenar tanto la calificacion numerica (`final_grade`) como la letra (`final_grade_letter`) en el modelo `SubjectCycle`. La letra se deriva del rango numerico:
- **A**: 90 - 100
- **B**: 80 - 89
- **C**: 70 - 79
- **D**: 60 - 69
- **F**: ≤ 59

El modelo incluye choices para la letra (`A`, `B+`, `B`, `C+`, `C`, `D`, `F`), aunque el README solo menciona A-F. La conversion parece ocurrir en el serializador o en la logica de registro del profesor.

## Por Que

- **Rendimiento de lectura**: Evita recalcular la letra en cada request; se calcula una vez al registrar la nota.
- **Flexibilidad**: Permite almacenar letras intermedias (`B+`, `C+`) si el sistema las soporta en el futuro.
- **Consistencia**: Todos los consumidores de la API (frontend, reportes PDF) leen el mismo valor de letra sin necesidad de reimplementar la logica.

## Opciones Descartadas

- **Calculo en tiempo real (property)**: Rechazado porque requeriria recalcular en cada serializacion y complicaria los reportes PDF.
- **Solo letras, sin numero**: Rechazado porque el profesor necesita registrar la nota numerica exacta.
- **Calculo puramente en el frontend**: Rechazado para evitar inconsistencias si el frontend y el backend usaran rangos ligeramente diferentes.

## Impacto en el Sistema

- El campo `final_grade_letter` en `SubjectCycle` es redundante con `final_grade` pero justificado por rendimiento.
- Existe una discrepancia: las choices del modelo incluyen `B+` y `C+`, pero la documentacion del README solo menciona A, B, C, D, F. Esto puede generar confusion si el frontend y el backend manejan escalas distintas.
- El retiro de materia usa la letra "R", que no esta en las choices del modelo. Esto funciona porque Django permite asignar valores fuera de choices a nivel de ORM, pero podria romper validaciones de serializadores o formularios si se usan `ChoiceField` estrictos.
- El modelo `Student` tiene `overall_gpa` y `last_gpa`, pero no esta claro si estos campos se actualizan automaticamente cuando cambian las `SubjectCycle` o si requieren recalculo manual.
