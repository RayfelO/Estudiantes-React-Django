---
title: "Modulo Frontend"
date: 2026-06-05
tags: [proyecto, sga, modulo]
aliases:
  - Sistema de Gestion Academica
---

# Modulo Frontend

Responsabilidad principal: Proporcionar la interfaz de usuario como Single Page Application (SPA) React, permitiendo a estudiantes y profesores interactuar con el sistema academico. La UI fue originalmente disenada en Figma y convertida a codigo mediante una herramienta de exportacion (Figma-to-React).

## Componentes Clave

### Paginas (Pages)
- `LogIn`: Pantalla de inicio de sesion unificada. Detecta el tipo de usuario a partir del JWT y redirige al dashboard correspondiente.
- `DashboardEstudiante2`: Panel principal del estudiante. Punto de entrada a seleccion de materias, calificaciones y perfil.
- `SeleccionEstudiante` / `SeleccionEstudiante1` / `SeleccionEstudiante2`: Flujo de seleccion de materias dividido en multiples pasos (wizard-like).
- `MateriasEstudiante`: Vista de las materias inscritas por el estudiante en el ciclo actual.
- `CalificacionesEstudiante`: Vista de calificaciones historicas con conversion a letras.
- `VerPerfil`: Perfil del usuario logueado (estudiante o profesor).
- `DashboardProfesor`: Panel principal del profesor. Acceso a asignaturas, estudiantes y registro de calificaciones.
- `REGISTRARUSUARIO`: Pantalla de recuperacion de contrasena (nombre confuso, posible remanente de codigo autogenerado).
- `LANDINGPAGEHOME`: Pagina de aterrizaje publica del sistema.

### Componentes Reutilizables
- `EditTable` / `TableEditor`: Componentes para edicion tabular de datos, usados principalmente por el profesor para registrar calificaciones.

### Dependencias Clave
- `react-router-dom`: Enrutamiento condicional basado en claims del JWT.
- `axios`: Comunicacion con la API REST de Django.
- `react-bootstrap` + `bootstrap`: Estilizacion de componentes.
- `chart.js` + `react-chartjs-2`: Visualizacion de estadisticas academicas.
- `@react-pdf/renderer`: Generacion de reportes academicos en PDF.

## Dependencias
- Consume la API REST expuesta por [[Modulo Estudiante]] y [[Modulo Profesor]].
- Depende del claim `is_student` / `is_professor` del JWT para enrutamiento y permisos de UI.

## Observaciones
- El codigo esta fuertemente hardcodeado: nombres de clases CSS autogenerados, SVG inline, rutas harcodeadas en `App.js`, y titles/meta vacios en el switch de rutas.
- La estructura es pagina-centrica (cada vista es un archivo grande con estilos embebidos) en lugar de componentes reutilizables.
- No existe manejo centralizado de estado (Context/Redux); cada pagina maneja su propio estado local.
- La autenticacion JWT se almacena en `localStorage` o `sessionStorage` (inferido por el patron de uso de axios con token).
- Los estilos estan en archivos `.module.css` autogenerados con nombres no semanticos (ej. `SeleccionEstudiante2.module.css`).
