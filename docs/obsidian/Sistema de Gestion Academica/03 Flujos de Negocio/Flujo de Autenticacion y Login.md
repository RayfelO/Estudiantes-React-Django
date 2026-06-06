---
title: "Flujo de Autenticacion y Login"
date: 2026-06-05
tags: [proyecto, sga, flujo]
aliases:
  - Sistema de Gestion Academica
---

# Flujo de Autenticacion y Login

Flujo operativo que permite a estudiantes y profesores acceder al sistema mediante credenciales unificadas y ser redirigidos a sus respectivos dashboards.

## Paso a Paso

1. **Acceso a la landing page**
   - El usuario llega a `/landing-page` o a `/` (login).
   - [[Modulo Frontend]] renderiza `LogIn`.

2. **Ingreso de credenciales**
   - El usuario ingresa `username` y `password`.
   - El frontend envia un POST a:
     - `/api/v1/students/login/` si selecciona rol estudiante.
     - `/api/v1/professor/login/` si selecciona rol profesor.
   - En la practica, ambos endpoints son `TokenObtainPairView` con serializadores distintos ([[Modulo API Estudiante]] y [[Modulo API Profesor]]).

3. **Validacion y emision de token**
   - El backend valida las credenciales contra el modelo `User` ([[Modulo Core]]).
   - Si son validas, emite un par de tokens JWT (`access` y `refresh`).
   - El token `access` incluye claims custom:
     - `is_student: true` / `is_professor: true`
     - `profile_id`: ID del perfil (Student o Professor).

4. **Almacenamiento y redireccion**
   - El frontend almacena el token (probablemente en `localStorage`).
   - Lee el claim `is_student` o `is_professor`.
   - Redirige a:
     - `/dashboard-estudiante2` para estudiantes.
     - `/dashboard-profesor` para profesores.

5. **Recuperacion de contrasena**
   - Desde la pantalla de login, el usuario puede acceder a `/recuperar-contraseña`.
   - Se envia un POST a `/api/v1/students/reset-password/` con el email.
   - El backend busca el usuario por email y actualiza la contrasena directamente (sin token de reseteo). [[Modulo API Estudiante]].

## Entidades y Estados Importantes

- `User`: Estado `active/inactive`. El login falla si el usuario no existe o la contrasena no coincide.
- JWT `access`: Tiempo de vida de 180 dias (configurado en settings.py). Muy prolongado para un entorno academico.
- JWT `refresh`: Tiempo de vida de 365 dias.

## Modulos Involucrados
- [[Modulo Core]] (modelos User, autenticacion JWT).
- [[Modulo API Estudiante]] (`StudentLoginView`, `PasswordResetView`).
- [[Modulo API Profesor]] (`ProfessorLoginView`).
- [[Modulo Frontend]] (`LogIn`, manejo de tokens, redireccion).

## Diagrama Mental

```
Usuario -> Landing Page -> Login
                |
                v
        +-------+--------+
        |                |
   Estudiante        Profesor
        |                |
        v                v
   /students/login   /professor/login
        |                |
        v                v
   Token JWT (claims)   Token JWT (claims)
        |                |
        v                v
   Dashboard Est.    Dashboard Prof.
```
