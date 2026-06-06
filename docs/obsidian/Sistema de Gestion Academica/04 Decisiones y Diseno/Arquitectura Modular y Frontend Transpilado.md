---
title: "Arquitectura Modular y Frontend Transpilado"
date: 2026-06-05
tags: [proyecto, sga, decision]
aliases:
  - Sistema de Gestion Academica
---

# Arquitectura Modular y Frontend Transpilado

## Problema

El equipo necesitaba una UI moderna y consistente pero no tenia un desarrollador frontend especializado en CSS/React. Disenaron toda la interfaz en Figma y necesitaban convertir ese diseno a codigo funcional rapidamente.

## Opcion Elegida

Usar una herramienta de exportacion Figma-to-React (probablemente un plugin como Anima o similar) para transpilar los mockups de Figma directamente a componentes React con CSS Modules. El codigo generado fue integrado tal cual en el proyecto.

## Por Que

- **Velocidad de desarrollo**: Evito la necesidad de escribir manualmente cada componente y estilo.
- **Fidelidad al diseno**: La UI es casi identica al prototipo de Figma.
- **Demo academico**: Como no era un proyecto de produccion, la deuda tecnica del codigo generado era aceptable.

## Opciones Descartadas

- **Desarrollo manual de componentes**: Rechazado por falta de tiempo y expertise en CSS avanzado del equipo.
- **Uso de un UI Kit existente (Material-UI, Chakra)**: Rechazado porque querian una estetica custom disenada especificamente para el proyecto.
- **Tailwind CSS**: Rechazado porque el equipo ya habia invertido tiempo en Figma y buscaba un camino directo de diseno a codigo.

## Impacto en el Sistema

- El codigo frontend es dificil de mantener: nombres de clases autogenerados, estilos hardcodeados, componentes monoliticos (una pagina = un archivo gigante).
- No hay separacion clara entre logica de presentacion y logica de negocio.
- Los assets (SVGs, imagenes) estan inline o duplicados en `public/`.
- El SEO es inexistente (titles y meta descriptions vacios).
- La navegacion esta harcodeada en `App.js` con un switch de rutas sin lazy loading.
- Cada pagina tiene su propio `.module.css` con nombres no semanticos (ej. `SeleccionEstudiante2.module.css`).
- Sin embargo, el resultado visual es profesional y cumplio perfectamente con los objetivos del proyecto academico.
