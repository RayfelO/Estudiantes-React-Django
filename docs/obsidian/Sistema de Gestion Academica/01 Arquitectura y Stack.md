---
title: "01 Arquitectura y Stack"
date: 2026-06-05
tags: [proyecto, sga, arquitectura]
aliases:
  - Sistema de Gestion Academica
---

# 01 Arquitectura y Stack

## Stack Tecnologico

| Capa | Tecnologia | Version | Proposito |
|------|-----------|---------|-----------|
| Backend | Django | 4.1.7 | Framework web principal |
| API | Django REST Framework | 3.14.x | Construccion de API REST |
| Auth | Simple JWT | 5.2.x | Autenticacion basada en tokens JWT |
| Docs | drf-spectacular | 0.26.x | Documentacion OpenAPI/Swagger auto-generada |
| CORS | django-cors-headers | 3.14.x | Manejo de CORS para el frontend |
| Env | django-environ | 0.10.x | Gestion de variables de entorno |
| Frontend | React | 18.2.x | Biblioteca de UI |
| Routing | React Router | 6.2.x | Enrutamiento del SPA |
| UI | React Bootstrap | 2.7.x | Componentes de UI estilizados |
| HTTP | Axios | 1.3.x | Cliente HTTP |
| Charts | Chart.js | 4.2.x | Visualizacion de datos y reportes |
| PDF | @react-pdf/renderer | 3.1.x | Generacion de reportes en PDF |

## Modulos de la Solucion

- **[[Modulo Core]]**: Modelos centralizados, permisos custom, serializadores y autenticacion. Es el nucleo del dominio.
- **[[Modulo Estudiante]]**: Endpoints especificos para el actor estudiante (perfil, calificaciones, record academico, login).
- **[[Modulo Profesor]]**: Endpoints para el actor profesor (perfil, asignacion de calificaciones, revision de notas, listado de asignaturas).
- **[[Modulo Ciclos Academicos]]**: Logica de seleccion de materias por parte del estudiante dentro de un ciclo academico.
- **[[Modulo Asignaturas por Ciclo]]**: Gestion de calificaciones numericas y letras, retiro de materias.
- **[[Modulo Frontend]]**: SPA React generado desde Figma con flujos independientes para estudiante y profesor.

## Patrones de Arquitectura

- **Monolitico modular**: Django con apps separadas por dominio (student, professor, academic_cycle, subject_cycle).
- **API REST**: Comunicacion frontend-backend via JSON sobre HTTP.
- **Autenticacion stateless**: JWT tokens con simplejwt, sin sesiones de servidor.
- **Modelo centralizado**: Todos los modelos de dominio viven en `core.models`, las apps solo contienen vistas y URLs.
- **Permisos basados en roles**: Custom permissions (`IsStudent`, `IsProfessor`, `ReadOnlyProfessor`) validan el tipo de usuario.
- **Frontend transpilado**: UI disenada en Figma y convertida a React, resultando en estilos hardcodeados y componentes pagina-centricos.
