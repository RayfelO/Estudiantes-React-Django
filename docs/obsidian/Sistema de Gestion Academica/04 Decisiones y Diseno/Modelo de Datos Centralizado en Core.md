---
title: "Modelo de Datos Centralizado en Core"
date: 2026-06-05
tags: [proyecto, sga, decision]
aliases:
  - Sistema de Gestion Academica
---

# Modelo de Datos Centralizado en Core

## Problema

El proyecto Django tiene multiples apps (`student`, `professor`, `academic_cycle`, `subject_cycle`), cada una con su propio dominio. Sin embargo, todos los modelos estan en `core.models`, y las apps solo contienen vistas y URLs. Esto rompe la convencion Django de que cada app tenga sus propios modelos.

## Opcion Elegida

Mantener todos los modelos de dominio (`User`, `Student`, `Professor`, `Subject`, `AcademicCycle`, `SubjectCycle`, `Message`, `Career`, `DocumentType`) en la app `core`. Las demas apps (`student`, `professor`, etc.) solo definen vistas, URLs y a veces tests.

## Por Que

- **Evitar importaciones circulares**: Si `Student` esta en `student.models` y `Professor` en `professor.models`, y ambos necesitan referenciar `Subject`, las dependencias circulares se vuelven problematicas.
- **Centralizar serializadores**: Los serializadores de DRF tambien viven en `core.serializers`, permitiendo reutilizacion entre apps.
- **Sencillez para el equipo**: Como proyecto academico, centralizar reduce la curva de aprendizaje y evita problemas de migraciones entre apps.

## Opciones Descartadas

- **Modelos distribuidos por app**: La convencion Django estandar. Rechazada por el riesgo de circular dependencies y la complejidad de manejar ForeignKeys cruzadas entre apps.
- **Patron Repository / Unit of Work**: Rechazado por ser overkill para un proyecto de scope academico con pocas entidades.

## Impacto en el Sistema

- El `AUTH_USER_MODEL` apunta a `core.User`.
- Las apps `student` y `professor` son esencialmente "controladores" sin modelo propio.
- Las migraciones de toda la base de datos estan concentradas en `core/migrations`, lo que simplifica el historial pero puede hacerlo mas pesado.
- Cualquier cambio en los modelos requiere reconstruir la app `core`, afectando potencialmente a todas las demas.
- Los permisos (`IsStudent`, `IsProfessor`) consultan modelos de `core`, consolidando la dependencia.
