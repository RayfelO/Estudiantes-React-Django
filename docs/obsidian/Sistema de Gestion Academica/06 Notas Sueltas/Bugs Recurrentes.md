---
title: "Bugs Recurrentes"
date: 2026-06-05
tags: [proyecto, sga, notas]
aliases:
  - Sistema de Gestion Academica
---

# Bugs Recurrentes

Problemas conocidos, comportamientos inesperados y workarounds identificados durante el desarrollo y uso del sistema.

## Permisos

- **`ReadOnlyProfessor` tiene logica invertida**: La condicion `if user.is_authenticated: return False` hace que usuarios autenticados sean rechazados. Parece ser un bug donde deberia verificar `if not user.is_authenticated`. Sin embargo, en la practica, `ReadOnlyProfessor` se usa junto con otras clases de permiso o el comportamiento no se manifesto.

## Serializadores

- **Duplicados en `core/serializers.py`**: Existen dos clases llamadas `ProfessorSerializer` (lineas 86 y 152). Django/DRF carga la ultima definicion, pero puede causar confusion.

## Vistas

- **`ProfessorSubjectViewStudent` usa `serializers_class` en lugar de `serializer_class`**: Funciona porque el atributo se accede manualmente, pero viola la convencion de DRF.
- **Manejo de excepciones generico**: Casi todas las vistas retornan `"Something went wrong"` para cualquier `Exception`, ocultando el error real. Para debug, se debe imprimir `e` o usar logging.
- **`SubjectList` usa `request = request.user`**: Sobrescribe el objeto request con el usuario, lo cual es un error semantico pero no genera excepcion porque `request` no se usa despues en ese metodo.

## Modelos

- **`final_grade_letter` no valida "R"**: El retiro de materia asigna la letra "R", pero esta no esta en las choices del modelo. Puede causar problemas si se usa un ModelForm o un serializer con `ChoiceField` estricto.
- **`overall_gpa` y `last_gpa` no se recalculan automaticamente**: No hay signals ni logica en `save()` que actualice estos campos cuando cambian las `SubjectCycle`.

## Frontend

- **Titles y meta descriptions vacios**: El switch de rutas en `App.js` tiene `title = ""` y `metaDescription = ""` para todas las rutas. La pagina siempre muestra el title por defecto.
- **Nombres de rutas inconsistentes**: `/recuperar-contraseña` renderiza `REGISTRARUSUARIO`, que parece ser una pagina de recuperacion, no de registro.
- **Hardcoding de rutas API**: Las URLs del backend probablemente estan hardcodeadas en los componentes React (no hay servicio API centralizado visible).

## Workarounds

- Para debug, agregar `print(e)` temporalmente en los bloques `except Exception`.
- Para datos de prueba, usar el panel de admin de Django o cargar fixtures directamente en la base de datos SQLite.
- Si el frontend no conecta con el backend, verificar que `CORS_ALLOWED_ORIGINS` incluya `http://localhost:3000`.
